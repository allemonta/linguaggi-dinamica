# Tipizzazione

## Tipi di dato
Un tipo di dato è una proprietà, un attributo dei dati che permette al compilatore (o all'interprete) di verificare il corretto uso del dato medesimo nonché di conoscere la sua occupazione di memoria

La tipizzazione (in inglese type checking) è il processo in base al quale compilatore o interprete determina il tipo di un dato, sia esso dichiarato o prodotto/calcolato, in modo da poter effettuare i relativi controlli:
- vincoli su valori ammissibili
- operazioni consentite

## Conseguenze della tipizzazione
L'uso della tipizzazione comporta:
1. Sicurezza: può essere scovato codice privo di senso o illecito e a livello di compilazione riduce i rischi di errori o risultati inatteri a run-time
2. Ottimizzazione: se eseguito a livello di compilzione le operazioni di type checking possono fornire informazioni utili per applicare le tecniche di ottimizzazione in fase di generazione del codice
3. Astrazione: si pensi all'esempio stringa come sequenza di caratteri (con tipizzazione) e non come ad una sequenza di byte (senza tipizzazione)
4. Documentazione: l'espressività dei nomi dei tipi di dato possono servire come fonte di documentazione in quanto illustra la natura di una variabile e l'intento del programmatore
5. Modularità: l'uso appropriato dei tipi di dato consente di definire interfacce di programmazione (API) e le funzionalità di queste sono descritte dalle signature delle funzioni pubbliche che la compongono

## Type checking
Le operazioni di type checking che stabiliscono il tipo di dato associato a porzioni di codice e che ne verificano i vincoli (range valori ammissibili e operazioni consentite) possono avvenire (esistono anche soluzioni miste):
- A compile time: type checking statico (es. C/C++, Fortran, Pascal, GO); questo porta a migliori prestazioni per via delle ottimizzazioni e dei meno controlli a run-time
- A run-time: type cheking dinamico (es Javascript, Perl, PHP, Python, Ruby)

## Type checking statico