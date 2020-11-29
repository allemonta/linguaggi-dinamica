# Python_1-1

## Trattamento dei tipi
- A partire dalla versione 2.2 del linguaggio (in modo esclusivo dalla 3) ogni dato manipolato dal linguaggio è un oggetto, ovvero un'instanza di una qualche classe. Questo vale anche per le funzioni, le stesse classi e i tipi di dato che, in altri linguaggi, vengono detti primitivi (int, float, ...)
- Il tipo di dato di una variabile è determinato a run-time
- Il tipo di una variabile può cambiare, semplicemente perché cambia il legame (binding) della variabile; il tipo di una variabile, infatti, è il tipo dell'oggetto al quale il nome è legato
- Una variabile deve comunque essere definita prima di poter essere usata
- La definzione avviene nel momento in cui al nome viene assegnato un valore
- Per conoscere il tipo di una variabile: `type(<variabile>)`
- I tipi elementari che trovano spazio in qualsiasi altro linguaggio (`int`, `float`, `bool` e `str`) vengono chiamati impropriamente "semplici".
- Non esiste un tipo speciale per i singoli caratteri
- Per gli interi e i numeri in virgola mobile non sono previste lunghezze diverse (`short`/`int` e `float`/`double`)

## Immutable e hashable
Immutable vuol dire che i singoli componenti (di primo livello) non possono essere modificati. Hashable vuol dire che dell'oggetto possiamo calcolare un valore hash che (proprio perché l'oggetto è immutable) non cambierà nel tempo

## Stringhe e f-stringhe
- Le stringhe sono racchiuse tra doppi o singoli apici. 
- Possibilità di utilizzare l'escape di certi caratteri (es. `\'`)
- Accesso ai caratteri tramite indice (es. `stringa[0]`)
- Possibilità di avere stringhe su più righe (includendo i caratteri di newline). Queste iniziano e terminano con `'''` oppure `"""`
- Per far si che una stringa diventi formattata si mette una `f` davanti ad essa. Nella stringa ora si potranno inserire espressioni racchiuse tra graffe. Le espressioni possono essere opportunamente formattate
- Sono oggetti immutable e hashable

```py
a = 10
print(f"Dopo il {a} viene {a + 1}")
print(f"10/3={(10/3):.3f}")
```

## Operatori
- Addizione, sottrazione e moltiplicazione tra `int` e `float` (`+`, `-`, `*`)
- Operatori logici relazionali (`<`, `<=`, `==`, `>`, `>=`, `!=`)
- La divisione restituisce `float`
- `//` quoziente, `%` resto
- Una stringa moltiplicata per un numero `s*n` restituisce la concatenazione di `n` copie della stringa `s`
- Operatori logici: `and`, `or`, `not`
- Shift dx/sx di `n` posizioni dell'intero `a`: `a >> n`, `a << n` 
- And/or bitwise di interi: `a & b`, `a | b`

Si tenga presente che:
- per un'operatore logico, i valori `0`, `0.0`, `""` e `None` rappresentano `False`, mentre tutti gli altri valori rappresentano `True`
- per un operatore aritmetico, `False` rappresenta `0` mentre `True` rappresenta `1`

## Tuple
Sequenza di oggetti arbitrari. Supportano l'accesso diretto agli elementi mediante indice. Sono oggetti immutable e hashable
```py
T = (1, "A", False, [1,2], 3.14, "A")

print(T[0])

# Essendo T[0] immutable non si può fare!
T[0] = 4

# Essendo  T[3] una lista (mutable), si può fare!
T[3][1] = 4
```

## Liste
Sequenza di oggetti arbitrari. Supportano l'accesso diretto agli elementi mediante indice
```py
L = [1, "A", False, (1,2), 3.14, "A"]

print(L[0])

# Si può fare
L[0] = 4
```

## Insiemi
Insiemi così come definiti in matematica
```py
S = {1, "A", False, (1,2), 3.14, "A"}
```

## Dizionari
- Chiamati anche tabelle associative o array associativi. 
- Associazione fra etichette (chiamate anche label e chiavi) e oggetti. 
- Solo i dati hashable possono esserre usati come label nei dizionari
- Supportano l'accesso con chiave.

```py
D = {
    1: 1,
    "A": T,
    (1,2): 'una tupla'
    # [1, 2]: "una lista"       NO!
}

print(D["A"])
print(D[(1, 2)])
```

## Controllo di flusso
Non si usano le parentesi per individuare un blocco, ma bensì l'indentazione. Un blocco di comandi è individuato da uno stesso livello di indent.

Struttura if:
```py
if <condizione>:
    <blocco>
elif <condizione>:
    <blocco>
else:
    <blocco>
```

Struttura while:
```py
while <condizione>:
    <blocco>
```

Struttura for:
```py
for <id> in <iterabile>
    <blocco>
```

L'esecuzione di un costrutto iterativo può essere interrotta dall'esecuzione dell'istruzione break, come avviene in altri linguaggi

## Namespace 
I nomi visibili in un determinato punto del programma (il cosidetto ambiente) è determinabile dalla struttura del programma. In python l'ambiente è distribuito in strutture dati chiamate namespace. I namespace sono dizionari che associano oggetti python a identificatori simbolici. In quanto strutture dati potranno essere manipolati dal programma.

Esistono diversi namespace che coesistono durante l'esecuzione di un programma e che sono isolati l'uno dall'altro:
- Namespace built-in: contiene i nomi di particolari oggetti (es. `id` e `abs`)
- Ogni modulo di cui è composto un programma ha un proprio namespace globale
- Ogni funzione ha un proprio namespace locale

Il lexical scope in python si può tradurre nella regola LEGB (Local, Enclosing, Global, Build-int). L'ordine di ricerca può essere in parte modificato utilizzando le definizioni `global` e `nonlocal`

```py
def A():
    global x               # Si riferisce alla variabile definita a riga 12
    def B():
        global x           # Si riferisce alla variabile definita a riga 12
        nonlocal y         # Si riferisce alla variabile definita a riga 8
        x = 2              # Senza la definizione di riga 4 creerebbe una nuova variabile
        y = 3              # Senza la definizione di riga 5 creerebbe una nuova variabile
    y = 2
    print("Riga 9:",x,y)
    B()
    print("Riga 11:",x,y)
x = 1
y = 1
print("Riga 14:",x,y)
A()
print("Riga 16:",x,y)
```

Possiamo recuperare i namespace tramite le funzioni `locals()` e `globals()`. Essendo dizionari possiamo recuperare i loro valori `locals()['x']`

## Trusti vari
- Eliminare tutti i duplicati da una lista: `list(set(L))`
- Scambiare i dati di due variabili: `x, y = y, x`