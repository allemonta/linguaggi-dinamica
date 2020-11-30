# Python 5

## Metodo __call__
Il metodo `__call__(self)` viene invocato ogni volta che si usa il nome della variabile come metodo.

Esempio:
```py
class A:
    x = 0

    def __call__(self):
        self.x += 1
        return self.x

a = A()
print(a())
print(a())
print(a())
```

Il metodo può tornare utile quando si parla di decoratori. Se in una classe implementiamo il metodo `__call__(self, funzione)` possiamo usarla come decoratore. Logica che si può seguire: il decoratore `D` è un qualcosa che restituisce una funzione alla chiamata `D()`. Abbiamo quindi che `D` può essere una funzione che restituisce una funzione oppure una classe con metodo `__call__` che restituisce una funzione e che può essere richiamata con `D()`. 

Esempio:
```py
class decoratore:
    def __call__(self, funzione):
        print("Decoro la funzione...")
        newFunzione = lambda: 1 + funzione()
        return newFunzione

@decoratore()
def quattro():
    return 4

print(quattro())
print(quattro())
print(quattro())
```

L'esempio sopra è equivalente a:
```py
class decoratore:
    def __call__(self, funzione):
        print("Decoro la funzione...")
        newFunzione = lambda: 1 + funzione()
        return newFunzione

def quattro():
    return 4

dec = decoratore()
quattro = dec(quattro)

print(quattro())
print(quattro())
print(quattro())
```

Attenzione! Al contrario di quanto si possa pensare la funzione `__call__` sarà chiamata una sola volta, per decorare la funzione passata. Non si potrà quindi usare uno stato interno.

## Le classi come oggetti
In Python tutto è oggetto, e anche le classi sono oggetti. Un oggetto è un'istanza di una qualche classe e dunque anche le classi devono essere istanza di una qualche classe. Tale metaclasse è `type` ed è un elemento di singolarità, in quanto tale classe è anche il tipo di se stessa.

`type` è sia una funzione built-in sia una classe. Come funzione built-in, `type` accetta 1 o 3 parametri. In caso di un parametro, la funzione restituisce il tipo dell'oggetto. In caso di tre parametri, la funzione è in grado di "creare" una classe, specificando (1) il nome della classe, (2) le classi base, (3) il dizionario degli attributi.

Esempio:
```py
def f(self, z=0):
    pass

X = type('A', (), { 'x': 0 })
Y = type('B', (X,), { 'y': 1, '__init__': f })      # La virgola è obbligatoria per specificare che si tratta di una tupla. Alternativa: (X, object)

a = X()
```

`type` è anche una classe e implementa il metodo `__call__`, e quindi diventano equivalenti le scritture `type('foo')` e `type.__call__(type, 'foo')`. La scrittura `type.__call__` denota un metodo unbound (cioè non legato ad un particolare oggetto), per questo è necessario specificare type come primo parametro. Infatti se `a` è un oggetto della classe `A` e `m` è un metodo della classe, la scrittura `a.m(...)` individua un metodo bound mentre `A.m(...)` è unbound.

## Il processo di istanziazione di una classe