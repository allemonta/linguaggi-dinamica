# Python_1-2

## Funzioni
Non c'è distinzione tra procedure e funzioni: sono tutte funzioni e come tali restituiscono qualcosa. In assenza dell'istruzione `return` la funzione restituisce il valore speciale `None` che indica l'assenza di valori.

Trusto: la funzione può restituire più di un valore separando questi da virgola: in tal caso si sta restituendo una tupla.

```py
def funzione(x):
    return x-1, x+1

prec, succ = funzione(10)
```

Le funzioni sono first class object. Questo vuol dire che possono essere assegnate ad una variabile, possono essere passate come parametro ad un'altra funzione e possono essere il risultato restituito da una funzione.

Esempio:
```py
def square(x):
    return x*x

s = square
print(s(2))

print(type(square))
print(type(s))
```

Esempio funzione come parametro:
```py
def f(callback):
    print("Sono la funzione f, sto per richiamare la funzione di callback!")
    callback()

def g():
    print("Sono la funzione g!")

f(g)
```

Esempio funzione come valore di ritorno:
```py
def tabellina(numero):
    def multipli():
        for i in range(0, 10):
            print(i*numero)

    return multipli


tabellina3 = tabellina(3)
tabellina3()

tabellina(4)()
```

## Funzione anonime: lambda
Spiegazione con esempio
```py
raddoppia = lambda x: x*2

def f(callback):
    print(f"Sono la funzione f. callback(4)={callback(4)}")

f(lambda x: x*2)
f(raddoppia)
```

## Parametri
In python il passaggio dei parametri avviene per riferimento. Si utilizza più l'espressione "binding" tra un nome e il riferimento ad un oggetto.

Se si passa un oggetto immutable il passaggio è assimilabile a quello che in altri linguaggi viene chiamato passaggio per valore. Si può cambiare il legame locale ma non l'oggetto (che è appunto immutabile). Ogni tale cambiamento ovviamente non può modificare il legame esterno.

In breve:
- L'assegnamento di una valore ad un oggetto (sia mutable che immutable) cambia il binding locale ma non quello globale (es. `L = [1, 2, 3]`)
- La modifica di un valore mutable va a toccare il binding globale (es. `L.append(4)`)
- La modifica di un valore immutable va a toccare il binding locale ma non quello globale (es. `par = "bella"`)

## Parametri keyword
I parametri formali possono prevedere un valore di default: se in fase di chiamata il corrispondente argomento non viene indicato, nella funzione il binding iniziale sarà con l'oggetto presente nella definizione: `def funzione(..., <nome>=<oggetto>, ...)`. Il binding dei parametri con valore default (detti anche parametri keyword) può essere fatto secondo la posizione oppure specificando il nome

Esempio:

```py
def f(x, y=1):
    return x*y

f(2)
f(2, y=4)
f(2, 10)
```
Regole:
- I parametri "non keyword" vengono detti posizionali
- I parametri posizionali (obbligatori) devono sempre precedere quelli keyword
- L'interprete effettua dapprima il binding dei parametri posizionali
- Per i parametri keyword, se il nome non viene specificato, procede sempre con l'ordine dettato dalla posizione
- Se un parametro keyword (non necessariamente il primo) viene specificato usando il nome, allora tutti quelli che vengono dopo devono essere specificati in tal modo

## Numero indeterminato di parametri 
- Nella definizione di una funzione si può specificare che il numero di parametri è variabile
- La variabilità può riguardare tanto parametri posizionali quanto parametri keyword
- Un nome preceduto da un asterisco indica la presenza di 0 o più parametri posizionali (`*T` indica una tupla di parametri)
- Uome preceduto da due asterischi indica la presenza di 0 o più parametri keyword (`**D` indica un dizionario di parameteri `<nome variabile>: <valore>`)
- Ci possono anche essere parametri obbligatori

Esempio:
```py
def funzione(x, *lista, a=5, **dizionario):
    for var in lista:
        print(var)

    for k, v in dizionario.items():
        print(f"{k} --> {v}")

funzione(1, 'A', 10, 14, b=7, c=8, a=5)
```

## Docstring
Se si inserisce una stringa multi-line subito dopo la definizione di una funzione (o di una classe) questa verrà restituita come risultato dell'invocazione di `help` sulla funzione/classe. La stringa può essere recuperata (output leggermente diverso, provare) tramite l'attributo  `__doc__`

```py
def funzione(a, b):
    ''' Questa funzione fa il prodotto di due valori '''
    return a*b

help(funzione)
print(funzione.__doc__)
```

## Decoratori
Primo esempio di zucchero sintattico: forma alternativa più gradevole con cui specificare un'istruzione o un costrutto che comunque è già previsto dal linguaggio.

La scrittura
```py
def F(funzione):
    def inner(<parametri>):
        <blocco>

    return inner

@F
def G(<parametri>):
    <blocco>
```

Equivale a
```py
def F(funzione):
    def inner(<parametri>):
        <blocco>

    return inner

def G(<parametri>):
    <blocco>

G = F(G)
```

Esempio
```py
def decoratore(callback):
    def check(x):
        print("Sono la funzione inner del decoratore! Richiamo la funzione di callback con il parametro solo se positivo")
        if (x > 0):
            callback(x)

    return check

@decoratore
def decorata(x):
    print(f"Sono la funzione decorata e mi è stato passato il valore {x}")

decorata(4)
decorata(-2)
```

Esempio utile:
```py
from time import time
from time import sleep

def TimeIt(f):
    def Timed(*args,**kwargs):
        start_time = int(round(time() * 1000))
        res = f(*args, **kwargs)
        stop_time = int(round(time() * 1000))
        print(f"Elapsed time: {stop_time-start_time}ms")
        return res

    return Timed

@TimeIt
def aspetta(s):
    sleep(s)

aspetta(1)
```