## strutture sistema operativo
### servizi offerti dal sistema operativo
- Ogni S.O. fornisce **servizi**
![[Pasted image 20250228105707.png|600]]
#### servizi utili per gli utenti
- **User interface**
	- CLI (Command-Line Interface)
	- GUI (Graphics user Interface)
- **Program execution**
	- sistema deve essere in grado di caricare un programma nella emmoria ed eseguirlo
- **I/O operations**
	- operazioni di input/output
- **File-system manipulation**
	- manipolazione file e gestione permessi
- **Communication**
	- gestire comunicazione tra processi
- **Gestione degli errori**
	- errori hardware (CPU, memoria, I/O devices)
	- eccezioni software
#### servizi utili per il sistema
- **Resource allocation**
	- allocazione delle risorse per ogni processo in esecuzione
		- cpu
		- memoria
		- device I/O
		- file storage
- **Accounting/logging**
	- tenere traccia di operazioni eseguite da programmi e utenti diversi
	- può essere utilizzato a fini statistici o per gestione costo noleggio risorse computazionali
- **Protection and security**
	- ogni processo non deve poter interferire con altri processi o con risorse protette
	- autenticazione utenti nel sistema
### interfacce utente
#### cli
- Interfaccia **a caratteri**:
	- input di **comandi** che vengono **interpretati** ed **eseguiti**
- Alcuni comandi sono **integrati** nel terminali stesso (es ```dirs```)
- Altri comandi derivano dall'esecuzione di **programmi di sistema** (es ```ls```)
	- forniti dal sistema operativo
- Storicamente utilizzata nei computer
#### gUi
- Interfaccia **grafica**
	- ispirata da una scrivania da ufficio
	- interazione fatta con tastiera, mouse e monitor
- Inventata al Xerox PARC e adottata commercialmente con Apple Macintosh
- Distribuzione
	- **parte** del **sistema operativo** stesso (es. in MacOS e Windows)
	- opzionale e **esterna** (es. Linux: GNOME)
#### interfacce touch
- Utilizzata nei dispositivi mobili
- Sfruttano **gestures** come metodo di interazione

<div style="page-break-after: always;"></div>

### system call
- **Meta-interfaccia**
	- utilizzata dalle interfacce utente
	- offerta dal S.O. per comunicare con il Kernel
		- implementata in **C/C++**
		- è parte del **Kernel**
- Accesso a system call avviene tipicamente attraverso un **API**
	- libreria software che ne semplifica utilizzo
	- traduce **metodo** invocato ad un certo **numero** associato alla system call e la **invoca**
	- permette maggiore portabilità
	- es. **Windows API**, **POSIX API**
![[Pasted image 20250228110904.png|500]]
- Esempio funzionamento:
	- viene chiamata una system call attraverso l'**API**
	- **system call interface** traduce **metodo** a un certo **numero** associato alla system call
	- viene eseguito il codice associato alla system call in **kernel mode**
	- viene riattivata **user mode** e restituito output della chiamata
![[Pasted image 20250228112016.png|400]]
#### passaggio parametri alla system call
- Attraverso **registri**
	- veloce, numero limitato di parametri
- Attraverso lo **stack di sistema**
	- push/pop
- Attraverso la **memoria**
	- indirizzo di memoria salvato in un registro
#### tipi di system call
- ==Process management==
	- creare/terminare/caricare/eseguire processi
	- allocare e liberare memoria
	- gestione processi multipli
- ==File management==
	- operazioni con i file
	- gestione degli attributi
- ==Device management==
	- gestione periferiche
	- modifica attributi
	- lettura/scrittura su dispositivi I/O
- ==Information maintenance==
	- gestione timer, tempo, data
- ==Protection==
	- controllo accesso risorse e permessi utenti
- ==Communication==
	- comunicazione tra processi
		- attraverso **shared memory**
		- attraverso **message passing**(pacchetti mossi da sistema operativo)
#### esempio: stampare stringhe
- Comando ```printf()```
	- utilizza system call ```write()```
![[Pasted image 20250228113259.png|250]]
### servizi di sistema
- Forniscono un **ambiente** per **sviluppo** e **esecuzione** programmi
	- agevolano utilizzo funzionalità sistema operativo
	- sfruttano numerose system calls
- Eseguono in **user mode**, non kernel mode
#### principali categorie servizi di sistema
- ==File management==
	- servizi per manipolazione di file e cartelle
- ==File modification==
	- editor per creare e modificare file
	- comandi che permettono manipolazione di testo
- ==Programming-language support==
	- compilatori, assemblatori, debugger, interpreti
- ==Program loading and execution==
	- linkers, loaders, sistemi di debug runtime
- ==Status information==
	- permettono di ottenere metriche di sistema 
	- es. memoria disponibile, numero utenti, ecc
- ==Communications==
	- agevolano comunicazione tra processi
- ==Background services==
	- noti anche come **services**, **subsystems**, **daemons**
	- attivati all'avvio del sistema
	- rimangono attivi in background
### linkers e loaders
- Processo di **compilazione** programma
	- comando ```gcc```
	- **compilatore**
		- codice sorgente -> file oggetto
	- **linker**
		- insieme di file oggetto + librerie statiche -> eseguibile
- Processo di **esecuzione** di un programma (effettuato dal **loader**)
	- attivato attraverso **system call**
	- carica il codice eseguibile dal disco alla memoria
		- **relocation**: aggiorna gli indirizzi di memoria nel codice eseguibile
	- carica eventuali **librerie dinamiche** necessarie per l'esecuzione del programma
		- spesso vengono caricate tutte all'avvio del sistema operativo
### perché le applicazioni compilate non sono cross-platform
- Sistemi operativi differiscono per:
	- architettura **cpu**
	- api **system calls**
	- formato **file eseguibile**
- Sviluppo **cross-platform**
	- usare linguaggi **interpretati** (python)
		- più lento
	- usare una **VM** (come quella fornita da Java)
		- conversione codice oggetto in codice macchina a runtime
	- **ricompilare** per ogni sistema operativo
- **Application Binary Interface** (ABI)
	- fornisce un interfaccia a livello di codice macchina
	- utile per compilare programmi generati con linguaggi di programmazione diversi

<div style="page-break-after: always;"></div>

### tipi di kernel
#### simple monolithic
- **Kernel** fornisce **tutti** i servizi del S.O.
- Vantaggi
	- molto **efficiente** e ottimizzato
- Svantaggi
	- ogni volta che serve aggiungere drivers, devo **ricompilare** intero Kernel
	- se un piccolo componente dà errore, salta tutto
- Es. MS-DOS, prime versioni di UNIX
#### complex monolithic
- **Core** del kernel **monolitico**
- Possibilità di estensione Kernel attraverso **loadable kernel modules** (LKMs)
	- moduli eseguiti in **kernel mode**
	- ogni componente è indipendente
	- comunicano tra loro tramite interfacce
	- es. drivers, funzionalità aggiuntive
#### microkernel
- Kernel molto **piccolo** e minimale
- Possibilità di estendere funzionalità attraverso **servizi**
	- eseguiti in **user mode**
	- comunicano tra loro attraverso **message passing**
- Vantaggi
	- Kernel sicuro e affidabile
	- facilità di estensione
- Svantaggi
	- overhead comunicazione tra servizi (perché avviene in user mode)
- Es. componente **Mach** del Kernel **Darwin** di MacOS
#### hybrid systems
- I sistemi operativi moderni contengono un insieme ibrido di più approcci
	- le funzionalità principali sono implementate attraverso kernel monolitico
	- funzionalità aggiuntive attraverso moduli o microkernel