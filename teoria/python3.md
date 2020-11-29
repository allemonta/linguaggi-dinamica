# Python 3

## Classi predefinite e attributi
- I tipi `int`, `str`, `list`, `dict`, ecc sono classi predefinite
- Gli attributi sono coppie `<nome, valore>`, dove il valore è un oggetto Python
- Gli attributi di una classe sono accessibili mediante la notazione puntata, ossia qualificando il nome tramite il riferimento alla classe
- Nella maggior parte dei casi, i valori degli attributi di una classe sono funzioni (che in questi casi sono detti metodi)
- Gli argomenti di queste funzioni sono tipicamente oggetti della classe

## Classi definite da utente
Si possono definire nuove classi e ogni classe può avere superclassi da cui eredita gli attributi. In python la definizione din una classe può menzionare più superclassi (ereditarietà multipla). Una classe `A` è superclasse di `B` se `A` è una classe base di `B` oppure se è una superclasse di una classe base di `B` (se non eredita direttamente da essa). Se `A` è superclasse di `B` si dice anche che `A` è antenato di `B` e `B` è discendente di `A`. Se non vengono specificate superclassi, viene comunque ereditata la classe `object`.

La definizione di una classe Python richiede di specificare tre tipi di informazione
- Il nome della classe
- L'indicazione delle classi da cui eredita (classi base)
- Gli attributi che fanno parte della classe

```py
class <nome-classe>(<classi-base>):
    <attributi>
```

Esempio:
```py
class A:
    x = 1  

class B(A):
    y = 2

    def f():
        print("Hello!")
        
    def g():
        print(B.y)
```

La relazioni tra classi e superclassi viene visualizzata tramite un grafo diretto aciclico (DAG). Nell'esempio precedente possiamo accedere all'attributo `x` lungo la catena ereditaria anche da `B`.

A differenza di Java, Python non utilizza le tipologie di attributi (pubblici, privati, protetti). Gli attributi sono sempre accessibili tramite la qualificazione `oggetto.attributo`. Nell'esempio precedente per accedere all'attributo `y` lo si deve qualificare come `B.y`, altrimenti provoca errore.

## Ereditarietà multipla e methor order resolution (MRO)
In caso di ereditarietà multipla, l'insieme delle classi nelle quali cercare un attributo `x`, con la relazione di parentela, forma un Grafo Diretto Aciclico (DAG), una struttura che in generale non è lineare. Per effettuare la ricerca, l'interprete deve linearizzare le classi che formano i vertici di tale grafo. Tale sequenza è detta Method Order Resolution (MRO). Per ottenere l'MRO si una classe si può utilizzare il metodo `.mro()` su tale classe. Non tutti i DAG possono essere linearizzati in Python

Esempio:
```py
class D: pass
class E: pass
class A(D): pass
class B(E): pass
class C(A,D,B): pass

print(C.mro())
```

## Processo di linearizzazione (costruzione dell'MRO)
Si veda terminologia presente negli appunti, n. 3.

Sono due le proprietà specifiche che devono essere soddisfatte dalla linearizzazione:
1. Monotocità: Se `A` precede `B` nella linearizzazione di `C`, allore `A` precede `B` nella linearizzazione di ogni sottoclasse di `C`.
2. Regola della precedenza locale: l'ordine delle classi base presenti nella definizione di una classe `C` è preservato nella realizzazione di `Gc`

Per il resto la linearizzazione viene eseguita seguento un ordine depth-first/left-to-right

## Algoritmo di linearizzazione
1. Se `C` è la classe `object`, allora `L(C) = object`.
2. Se `C` ha classi base `B1, B2, ..., Bk` allora `L(C) = C + merge(L(B1), L(B2), ..., L(Bk))`

Per calcolare `M = merge(L(B1), L(B2), ..., L(Bk))` possiamo semplificarla in questo modo: partendo dalle teste di `L(B1)` fino ad arrivare a `L(Bk)` si prende la prima classe che non compare nelle code degli altri e la si porta fuori, eliminandola da eventuali occorrenze nelle altre linearizzazioni.

Esempio pratico
```py
L(B) = BDEO
L(C) = CDFO 
L(D) = BC

L(A) = A + merge(L(B), L(C), L(D)) =
    = A + merge(BDEO, CDFO, BC)         # Check su B: ok, non compare nelle code di L(C) e L(D)
    = AB + merge(DEO, CDFO, C)          # Check du D: no, compare nella coda di L(C). Check su C: ok
    = ABC + merge(DEO, DFO, [])         # Check su D: ok
    = ABCD + merge(EO, FO, [])          # Check su E: ok
    = ABCDE + merge(O, FO, [])          # Check su O: no. Check su F: ok
    = ABCDEF + merge(O, O, [])          # Checl su O: ok
    = ABCDEFO
```

Esistono però situazioni paradossali, si pensi a `M = merge(ABO, BAO)`