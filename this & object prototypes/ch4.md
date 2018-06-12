# You Don't Know JS: *this* & Object Prototypes
# Chapter 4: Mixing (Up) "Class" Objects

## Class Theory
L'Object Oriented (OO) or Class Oriented programming, si basa sul concetto secondo noi "generalizziamo" un concetto e gli applichiamo delle caratteristiche comuni, che non ha senso riscrivere ogni volta.

Es. il tipo di dato String. Queste stringhe ogni volta saranno un dato diverso, ma sappiamo che in genere quello che ci può sempre interessare è il VALORE, un METODO per sapere la LENGTH, un Metodo per aggiungere in coda qualcosa... se ho un oggetto String saprò che potrò sempre applicare quegli stessi metodi, senza doverli riscrivere per ogni stringa.

Altro concetto è l'inheritance. Definiamo la classe Macchina, perchè può avere 2,4 o 5 posti, 4 ruote, un metodo per trasportare le persone. Ma la macchina a sua volta può essere una sottoclasse di un concetto Veicolo, che ha un motore, un carburante, un metodo per essere guidato.

`Polimorfismo` vuol dire che la sottoclasse, la classe figlia, può fare un override di un metodo della categoria madre. Quindi Veicolo e Macchina possono avere un metodo che ha lo stesso nome es. Accensione, ma quando creo una istanza di Macchina, il metodo accensione() di Macchina è più specifico, e sovrascrive il metodo accensione() di Veicolo.

## Javascript "Classes"
Javascript ha davvero le classi? NO.
Tutta la sintassi delle classi in Javascript nasce dal tentativo di soddisfare i programmatori Java che programmano Object Oriented e si sentono più a loro agio con le Classi per ordinare il loro codice. Si tratta solo di "Syntactic sugar".

## Class Mechanics
Nei linguaggi Class oriented si parte dal concetto di una struttura di base di classe `Stack`. Questa classe poi avrà dei metodi richiamabili (push, pop...) e delle proprietà, ma è un concetto astratto su cui non si può operare direttamente. Bisogna `istanziare` la classe Stack in un oggetto concreto, su cui poi operare.

### Building
Un paragone che si usa per spiegare il concetto è quello di una costruzione. La definizione della Classe corrisponde al progetto per realizzare un edificio, tutte le caratteristiche, i materiali, la struttura.
Per entrare però in un edificio bisogna poi in verità costruirne uno vero, in programmazione si dice `istanziare`, quindi a partire dalla Classe astratta istanziare un oggetto concreto, che copia tutte le caratteristiche del progetto.

### Constructor
Le istanze delle classi sono creati attraverso un metodo speciale della classe, di solito con lo stesso nome della classe, chiamato constructor. 

### Class Inheritance
Nei linguaggi Class Oriented, non solo puoi definire una classe che può essere istanziata da sola, ma puoi anche definire una classe che eredita dall'altra (child class e parent class).

La classe figlia è inizialmente una copia della classe madrem pa poi può sovrascrivere proprietà e metodi per conto proprio (override). Vediamo l'esempio di prima di Veicolo e Macchina:
```
class Vehicle {
	engines = 1

	ignition() {
		output( "Turning on my engine." )
	}

	drive() {
		ignition()
		output( "Steering and moving forward!" )
	}
}

class Car inherits Vehicle {
	wheels = 4

	drive() {
		inherited:drive()
		output( "Rolling on all ", wheels, " wheels!" )
	}
}

class SpeedBoat inherits Vehicle {
	engines = 2

	ignition() {
		output( "Turning on my ", engines, " engines." )
	}

	pilot() {
		inherited:drive()
		output( "Speeding through the water with ease!" )
	}
}
```

### Multiple Inheritance
E' possibile impostare più di una classe come classe genitore da estendere, ma sorge il problema di compatibilità. Cosa succede se tutte e due le classi genitore hanno un metodo che si chiama allo stesso modo? quale viene applicato?
Javascript non consente la multiple inheritance.

## Mixins
In javascript come abbiamo visto non esiste il concetto di classe astratta che diventa concreta in un oggetto che la copia. In javascript in realtà non esistono Classi, ma solo oggetti, e gli oggetti non si copiano mai davvero, sono copie per reference.

Allora come fanno gli sviluppatori JS a simulare questa copia classe->oggetto? Esistono due tipi di "mixin": esplicito e implicito

### Explicit Mixins
Creaiamo un'utility che di solito si chiama `extend()` ma qui chiamiamo mixin che copia manualmente le proprietà degli oggetti:
```
function mixin( sourceObj, targetObj ) {
	for (var key in sourceObj) {
		// only copy if not already present
		if (!(key in targetObj)) {
			targetObj[key] = sourceObj[key];
		}
	}

	return targetObj;
}

var Vehicle = {
	engines: 1,

	ignition: function() {
		console.log( "Turning on my engine." );
	},

	drive: function() {
		this.ignition();
		console.log( "Steering and moving forward!" );
	}
};

var Car = mixin( Vehicle, {
	wheels: 4,

	drive: function() {
		Vehicle.drive.call( this );
		console.log( "Rolling on all " + this.wheels + " wheels!" );
	}
} );
```
Ricordiamo che stiamo facendo una copia di oggetti, non esistono classi in Javascript. le proprietà vengono copiate per valore, le funzioni invece vengono copiate per reference;

### "Polymorphism" Revisited
