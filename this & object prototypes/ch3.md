# You Don't Know JS: *this* & Object Prototypes
# Chapter 3: Objects

## Sintassi
Esistono due modi di creare oggetti: la forma literal e il constructor.

La forma literal:

```
var myObj = {
    key: value,
    ...
}
```

usando una funzione come constructor: 
```
var myObj = new Object();
myObj.key = value;
```

Non c'è differenza ma è molto più diffusa la forma literal, che consente anche di assegnare proprietà e metodi nello stesso momento.

## Tipo
L'oggetto è uno dei 6 tipi di variabile originali di JS, ma a differenza degli altri non è un "simple primitive". Solo null a volte viene considerato un oggetto perchè per un bug di JS facendo typeof di null restituisce object.

Esistono poi dei sottotipi di Object, tra cui ad esempio le funzioni:
* Oggetti
 * String
 * Number
 * Boolean
 * Object
 * Function
 * Array
 * Date
 * RegExp
 * Error

 Ognuna di queste funzioni può essere usata come constructor con l'operatore new, per creare un oggetto del sub-type in questione. Questi tipi di oggetto, hanno molti nomi simili alle variabili primitive, ma non bisogna confondere le cose:

 ```
 var stringaPrimitiva = "Questa è una stringa semplice (primitiva)";
 typeof stringaPrimitiva; // "string"
 stringaPrimitiva instanceof String; //false -> non è un oggetto con String come prototype

 var stringaOggetto = new String("Questo è un oggetto di tipo String");
 typeof stringaOggetto; // "object"
 stringaOggetto instanceof String; // true
```
L'oggetto stringa ha delle proprietà e dei metodi che gli derivano dal suo prototype String, e che la variabile primitiva non ha (es. length). Tuttavia a volte l'interprete javascript converte la nostra stringa semplice in stringa oggetto quando ci serve (coercion). In realtà quindi si può fare

```
var strPrimitive = "I am a string";

console.log( strPrimitive.length );			// 13

console.log( strPrimitive.charAt( 3 ) );	// "m"
```
**Si raccomanda quindi di usare quasi sempre i dati primitivi, e ricorrere all'oggetto solo quando ci serve in casi specifici**

## Contenuti
Solo in apparenza i dati dell'oggetto sono contenuti "dentro" l'oggetto, in realtà quello che è conservato dentro l'oggetto sono le references alle proprietà, i puntatori che ci riportano all'indirizzo di memoria in cui sono conservati.

```
var myObject = {
	a: 2
};

myObject.a;		// 2

myObject["a"];	// 2
```
La differenza principale tra i due metodi . e [ ] per accedere a una proprietà dell'oggetto, è solo che l'operatore . richiede nomi semplici, non posso fare myObject.super-Fun!, mentre posso accedere con myObject['super-Fun!'].

Altra differenza è che usando la formula [ ], posso avere una reference variabile [ myVar ].

### Computed Property Names
a partire da ES6 usando la formula [ ] in fase di creazione dell'oggetto, è possibile anche generare i nomi delle
references alle nostre proprietà o metodi:

```
var prefix = "foo";

var myObject = {
	[prefix + "bar"]: "hello",
	[prefix + "baz"]: "world"
};

myObject["foobar"]; // hello
myObject["foobaz"]; // world
```

### Property vs. Method
Le persone che provengono da altri linguaggi di programmazione, quando la proprietà di un oggetto corrisponde a una funzione, parlano di "metodo". In realtà in JS non ha molto senso, le funzioni non "appartengono" a nessun oggetto, si tratta semplicemente di una reference che un oggetto ha a una funzione.

### Arrays
Gli array sono un particolare tipo di oggetto, pensato per conservare liste di dati, che possono essere di ogni tipo
```
var myArray = ['stringa', 42, { obj: 'anche un oggetto' }, ['un altro', 'array'], function() { return true}];
```
essendo oggetti, nulla mi vieta di assegnargli altre proprietà:
```
var myArray = [ "foo", 42, "bar" ];

myArray.baz = "baz";

myArray.length;	// 3

myArray.baz;	// "baz"
```
da notare abbiamo aggiunto una proprietà baz, che però rimane separta dagli elementi dell'array. non vanno confuse le cose, infatti la lenght dell'array rimane 3. E' suggeribile non usare numeri però come chiave della proprietà, altrimenti si crea confusione con gli elementi dell'array chiamando myArray[3] uno e myArray['3'] l'altro.

### Duplicating Objects
Quando si dice come si duplica un oggetto, non si sa bene cosa si voglia fare. Fare una copia delle referenze, e se queste a loro volta hanno altre referenze? Si fa la copia anche di quelle?

Una soluzione riconosciuta per gli oggetti, che sono JSON-safe è quella di convertire l'oggetto in stringa JSON e poi riconvertire la stringa JSON in un nuovo oggetto.

```
var newObj = JSON.parse(JSON.stringfy(oldObj));
```

ES6 ha introdotto una copia "leggera" degli oggetti con la proprietà Object.assing(...). I parametri sono prima un oggetto target, in cui scrivere, e poi altri oggetti che vogliamo copiare nel nostro target. I dati primitivi sono copiati per valore, gli oggetti sono copiati per referenza. Quindi coincidono con i dati dell'oggetto o degli oggetti originali.

```
var newObj = Object.assign({}, oldObj);
```

### Property Descriptors
```
a partire dalla versione ES5 di JS possiamo accedere alle impostazioni delle proprietà di un oggetto: 
var myObject = {
	a: 2
};

Object.getOwnPropertyDescriptor( myObject, "a" );
// {
//    value: 2,
//    writable: true,
//    enumerable: true,
//    configurable: true
// }
```
Per cambiare questi valori writable, enumerable,configurable possiamo usare Object.defineProperty
```
var myObject = {};

Object.defineProperty( myObject, "a", {
	value: 2,
	writable: true,
	configurable: true,
	enumerable: true
} );

myObject.a; // 2
```
#### Writable
se una proprietà non è writable, non è possibile modificarla. In normal mode non ci sono errori, ma la proprietà rimane invariata, in strict mode invece abbiamo proprio un errore (TypeError)

#### Configurable
Se una proprietà non è configurable, non possiamo usare Object.defineProperty per cambiare i suoi Property Descriptors writable, configurable, enumerable, se ci proviamo otteniamo un TypeError

#### Enumerable
tiene fuori o include la proprietà dalle iterazioni in ciclo for..in

### Immutability
ES5 ha introdotto dei modi di rendere gli oggetti immodificabili, con qualche piccola differenza uno dall'altro.

Nota bene che se rendiamo un oggetto o una sua proprietà immutabile, rimane fissa solo la sua referenza, se ad esempio si tratta di un array, o di una funzione, questo array o funzione rimangono modificabili. 

#### Object Constant
se combiniamo di descriptors writable:false e configurable:false, si crea una costante, la proprietà non può essere cambiata, ridefinita o cancellata

#### Prevent Extensions
Possiamo impedire all'oggetto di aggiungere nuove proprietà. Quelle già presenti rimangono presenti e modificabili.
```
var myObj = {
	a: 2
};

Object.preventExtensions( myObject );

myObject.b = 3;
myObject.b; // undefined
```

#### Seal
Object.seal(...), applica su un oggetto preventExtensions che abbiamo visto prima, e configura tutte le proprietà in configurable:false. Quindi non sarà possibile cambiare descriptors alle proprietà presenti, e non sarà possibile aggiungere nuove proprietà

#### Freeze
Object.freeze(...) non solo applica seal e tutte le sue conseguenze, ma rende anche le proprietà writable:false. Quindi sarà impossibile anche ridefinire le proprietà esistenti. E' il tipo di Immutability più forte.

### [[Get]]
Quando richiediamo una proprietà ad esempio myObj.a viene effettuata una richiesta [[Get]] sull'oggetto. Se una proprietà non viene trovata il [[Get]] restituisce `undefined` che ricordiamoci è diverso da quando invece non veniva trovata una variabile e ottenevamo un `ReferenceError`.

### [[Put]]
Se la proprietà è già presente:
1. La proprietà è un accessor descriptor (una proprietà che ha un getter o setter)? Se sì invoca il setter se c'è.
1. La proprietà è writable:false? Se in non-strict mode errore silenzioso, in strict-mode TypeError
1. Altrimenti assegna il valore normalmente.

Se la proprietà invece non è presente:
Comportamento complesso che vedremo nel Capitolo 5.

### Getters & Setters
E' possibile fare un override del [[Get]] e [[Put]] sulle proprietà di un oggetto tramite delle funzioni "getters" (per il [[Get]]) e "setters" (per il [[Put]]);

La sintassi get  associa una proprietà dell'oggetto a una funzione che verrà chiamata quando la proprietà verrà richiesta RHS

```
var myObject = {
	// define a getter for `a`
	get a() {		//a è una funzione la dovrei richiamare con myObject.a() -> siccome ho impostato getter
					// posso fare myObject.a
		return 2;
	}
};

Object.defineProperty(
	myObject,	// target
	"b",		// property name
	{			// descriptor
		// define a getter for `b`
		get: function(){ return this.a * 2 },

		// make sure `b` shows up as an object property
		enumerable: true
	}
);

myObject.a; // 2

myObject.b; // 4
```
Ho cretato due funzioni getter per le proprietà myObject.a e myObject.b usando le 2 diverse sintassi possibili.

guardiamo quest'altro esempio:
```
var myObject = {
	// define a getter for `a`
	get a() {
		return this._a_;
	},

	// define a setter for `a`
	set a(val) {
		this._a_ = val * 2;
	}
};

myObject.a = 2;

myObject.a; // 4
```
In questo caso ho impostato anche una funzione setter:
1. ho una LHS su myObject.a allora si invoca la funzione set.
1. la funzione set a(val), passa come parametro val=2, e imposto una proprietà dell'oggetto myObject._a_ = val * 2 // 4. (il nome _a_ è una convenzione, potevo chiamare la proprietà in qualunque modo).
1. richiedo ancora myObject.a ma questa volta RHS. Viene invocata la funzione get a(), che mi restituisce il valore della proprietà myObject._a_ // 4 settato prima.

### Existence
Abbiamo visto poco fa che se cerchiamo di accedere a una proprietà di un oggetto che non esiste, il nostro [[Get]] restituisce "undefined". Come facciamo allora a sapere se una proprietà esiste o non esiste?
```
var myObject = {
	a: 2
};

("a" in myObject);				// true
("b" in myObject);				// false

myObject.hasOwnProperty( "a" );	// true
myObject.hasOwnProperty( "b" );	// false
```
La differenza tra questi due metodi è che il primo, `in` va a cercare se esiste la proprietà su tutta la prototype chain. Il secondo metodo invece cerca solo direttamente nell'oggetto.

NB. il metodo `in` cerca solo le proprietà di un oggetto, non è possibile utilizzarlo per cercare se un valore esiste o meno dentro un array.

#### Enumeration
il `for..in` loop applicato a un oggetto, scorre tutte le proprietà purchè non abbiano impostato tra i property descriptors "enumerable:false", in quel caso, anche se la proprietà esiste viene ignorata dal nostro loop.

Il for..in loop applicato ad un oggetto array, scorre non solo gli indici numerici (array in senso stretto), ma anche tutte le altre proprietà che sono "enumerable". Per questo, per evitare problemi per gli array è meglio utilizzate un ciclo loop normale con for e array length.

### Iteration
Diversamente da quanto accade per gli array, l'interazione dei valori di tutte le proprietà degli oggetti non è certa e può cambiare nei vari motori JS. Non è afidabile.

ES6 ha aggiunto un loop `for..of` per scorrere tutti gli elementi di un array senza farlo manualmente con un contatore e array length, oppure si può fare il loop dei valori delle proprietà di un qualunque oggetto se quest'ultimo definisce il suo custom iterator:

```
var myArray = [ 1, 2, 3 ];

for (var v of myArray) {
	console.log( v );
}
// 1
// 2
// 3
```
ma come funziona dietro le quinte? Gli array hanno di default una funzione @@iterator che viene chiamata da un metodo next() dell'oggetto, e che restituisce un oggetto iterator { value:..., done:... } la proprietà done true o false indica se il loop è finito o no.
Volendolo scrivere funziona così:
```
var myArray = [ 1, 2, 3 ];
var it = myArray[Symbol.iterator]();

it.next(); // { value:1, done:false }
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { done:true }
```

Gli array hanno di default questa funzione @@iterator, gli altri oggetti no. Se vogliamo definirne uno:
```
var myObject = {
	a: 2,
	b: 3
};

Object.defineProperty( myObject, Symbol.iterator, {
	enumerable: false,
	writable: false,
	configurable: true,
	value: function() {
		var o = this;
		var idx = 0;
		var ks = Object.keys( o );
		return {
			next: function() {
				return {
					value: o[ks[idx++]],
					done: (idx > ks.length)
				};
			}
		};
	}
} );

// iterate `myObject` manually
var it = myObject[Symbol.iterator]();
it.next(); // { value:2, done:false }
it.next(); // { value:3, done:false }
it.next(); // { value:undefined, done:true }

// iterate `myObject` with `for..of`
for (var v of myObject) {
	console.log( v );
}
// 2
// 3
```