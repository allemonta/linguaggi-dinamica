# Python 2

## Struttura iterabile
Un iterable è una struttura su cui si può iterare, cioè accedere a tutti gli elemeni secondo un preciso ordine. In altri termini è un contenitore ordinato di oggetti.

Il contenitore può anche essere virtuale; in questo caso gli oggetti vengono generati quando la struttura viene perorsa.

Esempi di strutture iterabili: liste, stringhe.

Dato un iterabile `R`, è possibile recuperare il corrispondente iteratore usando la scrittura `I = iter(R)`. Disponendo dell'iteratore gli elementi della struttura possono essere visitati uno ad uno mediante la scrittura `e = next(I)`.

Se si tenta di eseguire un numero di accessi superiore al numero di elementi della struttua viene sollevata un eccezione del tipo `StopIteration`.

## Iterabile range
`range(<valore-iniziale>, <valore-finale>, <passo>)`

- Con 2 parametri si ha `passo = 1`
- Con 1 parametro si ha `passo = 1` e `valore-iniziale = 0`

## Creazione iterabile
Per creare un iterabile tramite classe questo deve avere i metodi `__iter__(self)` che restituisce un'oggetto (il vero e proprio iterabile) che ha un metodo `__next__(self)` che restituisce il valore da iterare (o `StopIteration` se i valori sono finiti)

Esempio
```py
class squares:
    def __init__(self,n):
        self.n = n
        self.i = 0

    def __iter__(self):
        return self

    def __next__(self):
        if self.i == self.n:
            raise StopIteration

        t = self.i
        self.i += 1
        return t*t

for n in squares(10):
    print(n)
```


## List comprehension
Liste:
`[<espressione> for <id> in <iterabile> if <condizione>]`

esempio:
`dispari = [x for x in range(20) if x % 2 == 1]`

Dizionari:
`{<espressione> for <id> in <iterabile> if <condizione>}`

esempio:
`dispari = [f"valore{x}":x for x in range(20) if x % 2 == 1]`

## Generatore
Un generatore implementa lo steso protocollo di un iteratore ma è definibile in modo più semplice  a partire dalla nozione di funzione. Sono utilizzati i concetti di generator funzion e di generatore vero e proprio. 

La generator function è una funzione a tutti gli effetti, che accetta parametri in ingresso e restituisce un generator (object). Un generatore `g` viene poi invocato usando il metodo `next(g)`. In particolare:
- Il generatore non accetta parametri
- Fra una chiamata e la successiva il generatore conserva lo stato interno; si può quindi dire che questo riprende la computazione dal punto in cui la aveva interrotta

## Istruzione yield
Il modo in cui una funzione si configura come generator function è la presenza di una o più istruzioni `yield` al posto dell'istruzione `return`.

Esempio senza parametro:
```py
def fibonacci():
    a = 0
    b = 1
    while True:
        yield b
        a,b = b,a+b

F = fibonacci()

for _ in range(5):
    print(next(F))
```

Esempio con parametro:
```py
def fibonacci(n):
    a = 0
    b = 1
    for _ in range(n):
        yield b
        a, b = b, a+b

for f in fibonacci(5):
    print(f)
```

## Coroutine
La funzione `yield` è bidirezionale. Il suo uso generale è un assegnamento: `<variabile-in> = yield <espressione-out>`.

Il generatore restituisce un valore al chiamante e va in pausa. Dopo il generatore viene riattivato ricevendo un imput dal chiamante. Per inviare un valore al generatore è stata introdotta l'istruzione `send`.

Se al generatore passiamo valori di input esso cessa di essere un generatore e diventa una courotine. 

Esempio:
```py

def coroutine():
    print("Coroutine started")
    for i in range(1,6):
        x = (yield i**2)
        print(f"Message to coroutine {x}")

c = coroutine()                              
print("Coroutine created")
print(f"Message from coroutine {next(c)}") 

try:
    while True:
        x = input("Enter a value: ")
        print(f"Message from coroutine {c.send(x)}")
except StopIteration: pass
```

I thread sono pensati per l'esecuzione parallela (su CPU multicore) e sono gestiti dal SO, mentre le courotine implementano una concorrenza cooperativa e queste decidono autonomamente quando cedere (`yield`) il controllo. 

## Trusti vari
Dato un iterabile si possono ottenere tutti i suoi valori ad esempio (prendendo come riferimento `squares` così: `tuple(squares(10))`