# Python 3

## Protocollo di accesso agli agli attributi
Metodi rilevanti:
- `__getattribute__(self, <attributo>)`: viene invocato per accessi in lettura. Solleva un'eccezione `AttributeError` se l'attributo è mancante [metodo di `object`]
- `__getattr__(self, <attributo>)`: viene invocato quando si verifica un cache miss, cioè l'attributo non è presente [metodo NON di `object`]
- `__setattr__(self, <attributo>, <valore>)`: per modificare il valore di un attributo e definirlo se non ancora presente [metodo di `object`]

`A.__getattribute__('x')` non è equivalente a `A.x` (non è zucchero sintattico) in quanto il secondo modo, in caso di eccezione per cache miss e presenza del metodo `A.__getattr__(self, <attributo>)`, viene gestito dall'interprete che provvede a richiamare il metodo `A.__getattr__('x')`. 

Esempio:
```py
class A:
    def __getattr__(self, nome):
        return f"Attributo {nome} non esistente"

a = A()
print(a.x)
```

Si può ad esempio fare in modo che se un valore non esiste, questo venga istanziato con valore `None` o con qualsiasi valore di default e tenere traccia ad ogni accesso di attributo/metodo della classe.
```py
class A:
    def __getattribute__(self, attr):
        print(f"Eseguito l'accesso all'attributo {attr}")
        return super().__getattribute__(attr) 

    def __getattr__(self, attr):
        print(f"Attributo {attr} non esistente. Creazione...")
        self.__setattr__(attr, None)
        return None

a = A()
print(a.x)
print(a.x)
```

Come detto prima `a.x != a.__getattribute__('x')`. Se volessimo un comportamento uguale dovremmo gestire la possibile eccezione sollevata in caso di cache miss.
```py
class A:
    def __getattribute__(self, attr):
        try:
            return super().__getattribute__(attr) 
        except AttributeError:
            return self.__getattr__(attr)

    def __getattr__(self, attr):
        self.__setattr__(attr, None)
        return None

a = A()
print(a.x)
print(a.__getattribute__('w'))
```

## Descrittori
I descrittori vengono utilizzati per controllare l'accesso agli attributi di altre classi, sono quindi una generalizzazione delle property. Tecnicamente un descrittore è una classe in cui è presente la definizione di almento uno dei seguenti magic method:
1. `__get__(self, instance, cls)`
2. `__set__(self, instance, value)`
3. `__delete__(self, instance)`

Quando si cerca di leggere il valore di un attributo definito come descrittore viene eseguito il metodo `__get__`. Stesso discroso per il `__set__`.
```py
class descrittore:
    contGlobale = 0
    contLocale = {}

    def __get__(self, inst, cls):
        if not inst in self.contLocale:
            self.contLocale[inst] = 0

        self.contGlobale += 1
        self.contLocale[inst] += 1
        
        print(f"Tentativi di lettura globali: {self.contGlobale}")
        print(f"Tentativi di lettura locali: {self.contLocale[inst]}")
        return None

    def __set__(self, inst, cls):
        print("Stai tentando di scrivermi!")
        return None

class controllata:
    v = descrittore()

a = controllata()
b = controllata()

print(a.v)
print(a.v)
print(b.v)
print(b.v)
```

Altro esempio realistico:
```py
import hashlib
class keyring:
    def __init__(self):
        'Le coppie (password,chiave) sono memorizzate in un dizionario'
        self.passwords = {}
    def __get__(self,inst,cls):
        '''Per l'accesso viene richiesta la password, il cui valore hash deve
           coincidere con il valore hash memorizzato '''
        try:
            k,v = self.passwords[inst]
        except KeyError:
            print(f"Unknown user {inst.user}")
            return None
        passwd = input("Enter password: ")
        h = hashlib.sha256(bytes(passwd,'utf-8')).hexdigest()
        if k==h:
            return v
        print("Wrong password")
        return None
        
    def reset(self,inst,key):
        'Inserisce una nuova chiave e resetta la password'
        passwd = input("Enter new password: ")
        h1 = hashlib.sha256(bytes(passwd,'utf-8')).hexdigest()
        passwd = input("Repeat password: ")
        h2 = hashlib.sha256(bytes(passwd,'utf-8')).hexdigest()
        if h1!=h2:
            print('Passwords differ!')
        else:
            self.passwords[inst] = (h1,key)
            print("Password correctly set!")
            
    def __set__(self,inst,key):
        'Chiamata ogni volta che si cerca di inserire un nuovo valore della chiave'
        try:
            k,v = self.passwords[inst]
        except KeyError:
            self.reset(inst,key)
        else:
            passwd = input("Enter password: ")
            h = hashlib.sha256(bytes(passwd,'utf-8')).hexdigest()
            if h==k:
                self.reset(inst,key)
            else:
                print('Wrong password!') 

class user:
    def __init__(self,name):
        self.user = name
    key = keyring()

u1 = user("Mauro")
u1.key = "my secret key!"
print(u1.key)

u2 = user("Paolo")
u2.key = "Paolo's key"
print(u2.key)

# print(u1.key.passwords)      Non ci si può accedere
```