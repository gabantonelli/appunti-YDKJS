# You Don't Know JS: *this* & Object Prototypes
# Chapter 5: Prototypes

## `[[ Prototype ]]`
quasi tutti gli oggetti Javascript quando sono creati hanno una proprietà [[ Prototype ]] diversa da NULL.
Nel caso in cui una proprietà non fosse presente nell'oggetto, il sistema procederà a cercarlo nell'oggetto Prototype, ancora nell'oggetto prototype di quest'altro e via lungo tutta la catena fino all'ultimo genitore Object.prototype. Solo allora se non sarà trovata la proprietà avremo un valore undefined.

### Setting and Shadowing properties

