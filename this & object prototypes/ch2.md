# You Don't Know JS: *this* & Object Prototypes
# Chapter 2: `this` All Makes Sense Now!

## Call-site
per capire il comportamento di `this` bisogna capire dove è stata invocata la funzione (call-site). Dobbiamo capire i vari execution context creati, e di questi ci interessa l'ultimo, quello che conterrà il comando di eseguire la nostra funzione.

## Nessuna magia, solo regole

### Bending di Default
quando non si applica nessuna delle altre regole, ci troviamo di fronte a una funzione invocata da sé nel global space. In questo caso `this` si riferisce all'oggetto `global`.

```
function foo() {
	console.log( this.a );
}

var a = 2;

foo(); // 2
```

In questo caso `this` va a prendere l'oggetto global e di questo la proprietà a. Ricordiamo che tutte le variabili nel global scope sono accessibili anche come proprietà dell'oggetto global.

In caso di `strict mode` il default binding non funziona, quindi `this` corrisponderà a `undefined`.

### Implicit Binding

Un'altra regola da considerare: se il call-site corrisponde al metodo di un oggetto, in quel caso la parola this si riferisce a quest'oggetto.

```
function foo() {
	console.log( this.a ); //in questo esempio è quindi sinonimo di obj.a
}

var obj = {
    a: 4,
    foo: foo 
}

obj.foo(); //4
```

#### Implicitly Lost
A volte può essere frustrante che nel passare ancora la funzione, posso perdere l'Implicit Binding, e this può tornare a puntare all'oggetto global. Una soluzione è fissare all'interno della funzione il valore di this, sfuttando la copia by reference ed ho un indirizzo fisso a quell'oggetto.
```
function foo() {
    var self = this;
    function bar() {
        console.log( self.a ); 
    }
    bar();	
    }

var obj = {
    a: 4,
    foo: foo 
}

obj.foo();
```

## Explicit Binding
Tutte le funzioni, hanno tramite il loro prototype dei metodi che ci consentono di definire esplicitamente l'oggetto che vogliamo usare, quando usiamo this. Sono le funzioni `call()` e `apply()`;

```
function foo() {
    console.log(this.a);
}

var obj = {
    a: 3
}

foo.call( obj ); // 3 -- il this corrisponde all'oggetto obj, senza la funzione call, this avrebbe puntato a global
```
La funzione di `call()` e `apply()` è identica, cambia solo il modo di utilizzo. Se voglio passare altri parametri alla funzione, posso passarli, come parametri seguenti di call 

```
foo.call(obj, par1, par2); //utilizzo di call con parametri
foo.apply(obj, [par1, par2]); //utilizzo identico ma apply vuole come secondo parametro un array dei parametri da passare
```

### Hard binding
L'explicit binding non risolve il problema visto prima dell'Implicitly Lost, ma esiste un pattern che risolve il problema
```
function foo(){
    console.log( this. a);
}

var obj = {
    a: 2
}

var bar = function() {
    foo.call(obj); //facendo questo ho blindato l'esecuzione di foo sempre usando obj
}

//adesso in qualunque caso chiami bar, foo utilizzerà sempre sempre obj
bar(); //2
bar.call(window); //2
setTimeout(bar, 2000); //2
```

Questo pattern è utilizzato soprattutto per il currying di funzione -> creare copie di funzioni ma con uno o più parametri già impostati. A partire da ES5 è disponibile anche un metodo bind delle funzioni per fare esattamente questo

```
function multiply(a,b) {
    return a * b;
}

var multiplyByTwo = multiply.bind(this, 2); //in questo modo faccio copia della funzione multiply ma con parametro a impsotato a 2

console.log(multiPlyByTwo(6)); // 12

```

## "new" Binding
l'operatore "new" in JS è stato adottato solo per attrarre sviluppatori Java, ma in verità le funzioni Constructor non hanno niente a che vedere con Classi e istanze della classe.

In JS non esistono funzioni "constructor" che hanno qualcosa di speciale. Quelle che definiamo constructor qui sono funzioni qualsiasi che chiamiamo con una "constructor call" perchè ci mettiamo l'operatore "new" davanti.

quando una qualsiasi funzione viene chiamata con un new davanti succede questo:
1. viene creato un nuovo oggetto
1. il nuovo oggetto creato viene collegato al suo Prototype
1. il nuovo oggetto creato viene linkato al "this" della funzione
1. a meno che la funzione non richieda altro, viene restituito come output il nuovo oggetto constructed

## Quindi come si determina alla fine `this`, anche in base alla priorità delle binding rules?

1. La funzione è invocata con `new`? se sì, il nuovo oggetto è `this`
1. La funzione è invocata con `call` oppure `apply` (*explicit binding*)? se sì allora `this` è l'oggetto che passiamo noi
1. La funzione è chiamata come metodo di un oggetto (*implicit binding*)? se sì allora `this` corrisponde all'oggetto in questione
1. Altrimenti this corrisponde all'oggetto global (*default binding*), oppure a undefined in caso di "strict mode"

## Binding Exceptions

### Ignored `this`

se passiamo null, o undefined al metodo call, apply o bind, si applica il default binding

### Safer `this`
a volte per sicurezza, quando non ci interessa passare l'oggetto ma solo i parametri (es. currying di funzioni), possiamo usare null oppure un oggetto vuoto creato apposta: 
```
function foo(a,b) {
	console.log( "a:" + a + ", b:" + b );
}

// our DMZ empty object
var ø = Object.create( null );

// spreading out array as parameters
foo.apply( ø, [2, 3] ); // a:2, b:3

// currying with `bind(..)`
var bar = foo.bind( ø, 2 );
bar( 3 ); // a:2, b:3
```
### Indirection
Bisogna fare attenzione a non creare references indirette alle funzioni, altrimenti si va a applicare il default binding. Vediamo l'esempio:
```
function foo() {
	console.log( this.a );
}

var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };

o.foo(); // 3
(p.foo = o.foo)(); // 2
```

l'ultima riga di codice assegna a p.foo non a o.foo ma direttamente alla funzione sotto di essa, e invocandola lì chiamiamo direttamente la funzione, quindi si applica il default binding.

### Lexical `this`
E6 ha introdotto un tipo di funzione che non sta a queste 4 regole di binding: la funzione fat arrow =>

Con questo tipo di funzione il this non dipende dalle 4 regole, ma dall'enclosing scope.

```
function foo() {
    //restituisce una arrow function
    return (a) => {
        // 'this' qui è adottato lessicalmente da 'foo()' perchè sta nel suo blocco
        console.log( this.a );
    }
}

var obj1 = {
    a: 2
}

var obj2 = {
    a:3
}

var bar = foo.call(obj1); 
bar.call(obj2); // 2 non 3!!
```