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
