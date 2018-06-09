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
*Si raccomanda quindi di usare quasi sempre i dati primitivi, e ricorrere all'oggetto solo quando ci serve in casi specifici*

## Contenuti



