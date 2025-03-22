## processi
### concetto di processo
- **Programma**
	- elemento passivo
	- file eseguibile che risiede sul disco
- **Processo**
	- elemento attivo
	- programma in esecuzione
>[!info] Nota
>- Esistono programmi multi-processo (es. Chrome)
### allocazione memoria processo
- **Text**
	- contiene codice eseguibile
- **Data**
	- contiene variabili globali
	- si distingue _uninitialized_ e _initialized_ data
- **Heap**
	- usato per allocazione dinamica
- **Stack**
	- memoria allocata da chiamata di funzioni

![[Pasted image 20250307104335.png|400]]

>[!warning] Attenzione
>- Heap e stack crescono in modo opposto quindi vi deve essere un limite massimo per evitare che vadano ad interferire

### process control block (pcb)
- **Struttura dati** che contiene informazioni associate ad un processo
- Contiene:
	- **process state**
		- stato in cui si trova il processo
	- **program counter**
		- indirizzo istruzioni da eseguire
	- **CPU registers**
		- registri processo
	- **CPU scheduling information**
		- priorità e puntatori alla coda di scheduling
	- **memory-management information**
		- memoria allocata al processo
	- **accounting information**
		- metrice relative al processo
	- **I/O status information**
		- dispositivi I/O allocati per il processo

![[Pasted image 20250307104753.png|100]]
#### in linux
![[Pasted image 20250307105044.png|700]]
#### process table
- **Lista** concatenata bidirezionale che contiene i **PCB**
![[Pasted image 20250307105225.png|500]]
### process state
- Un processo può assumere 5 stati:
	- **new**
		- alla creazione del processo
	- **ready**
		- inserito in una coda in attesa di essere eseguito dal processore
	- **running**
		- in esecuzione
		- può essere forzatamente interrotto tramite interrupt del S.O.
	- **waiting**
		- in attesa di un evento
		- sarà poi riportato allo stato ready
	- **terminated**
		- ha terminato l'esecuzione
- Si distinguono processi:
	- **I/O-bound**
		- molte operazioni I/O
	- **CPU-bound**
		- prevalentemente esecuzione di istruzioni CPU
![[Pasted image 20250307105500.png|400]]
### process scheduling
- ==Process scheduler==
	- **sceglie** quale **processo** eseguire tra quelli in coda
- Nei processori con 1-core
	- 1 solo processo in esecuzione
	- tutti gli altri processi sono in attesa
- **Code** di scheduling:
	- implementate con liste concatenate
	- **Ready queue**
		- processi pronti in attesa di essere eseguiti
	- **Wait queues**
		- processi in attesa per evento (es. I/O)
		- ci possono essere più tipi di code wait in base al tipo di evento
![[Pasted image 20250307110048.png|400]]
<div style="page-break-after: always;"></div>

### context switch
- Avviene quando si **scambia** il **processo** in **esecuzione** con un altro
- Modalità in cui avviene lo scambio
	- salvate informazioni del **PCB** del processo in esecuzione
	- caricate le informazioni del **PBC** del nuovo processo
- Tempo di **overhead**
	- durante lo scambio la CPU rimane in idle
	- può essere ridotto attraverso un **supporto hardware** (es. registri multipli)
![[Pasted image 20250307110505.png|350]]
### creazione di un processo
- A partire da un processo **parent** vengono creati processi **children**
- Viene assegnato un **process identifier** (pid)
	- numero incrementale
- Alcuni sistemi operativi permettono il passaggio di opzioni relative a:
	- condivisione risorse
	- esecuzione
	- spazio di indirizzi
#### in linux
- **Creazione processo**
	- tramite system call: ```fork()```
		- al processo parent restituisce ```pid```$>0$ (nuovo pid associato al child)
		- al processo child restituisce ```pid```$=0$
	- il processo child è inizialmente una **copia** del processo parent
- Sostituzione codice in memoria processo child con quello del **nuovo eseguibile**
	- tramite system call: ```exec()```
- Il processo parent rimane (opzionalmente) in **attesa** del processo child 
	- tramite system call: ```wait()```
![[Pasted image 20250307111303.png|500]]
![[Screenshot 2025-03-07 alle 11.13.29.png|400]]
#### albero di processi
- Esiste un **processo iniziale** da cui si generano tutti gli altri processi
	- in linux ```systemd``` (con ```pid=1```)
![[Screenshot 2025-03-07 alle 11.20.11.png|350]]

<div style="page-break-after: always;"></div>

### terminazione di un processo
- Avviene quando finisce di eseguire l'ultima istruzione o viene forzatamente interrotto
- ```exit()```
	- system call chiamata dal **processo** per permettere la **terminazione**
	- restituisce dati sullo stato al processo parent
- ```abort()```
	- permette al **parent** di terminare processo **child**
- ```wait()```
	- permette di **aspettare** la **terminazione** del processo children
		- poi pulisce memoria child
	- ```pid=wait(&status)```
		- permette di conoscere valore con cui è terminato processo child
- Processo **zombie**
	- child termina senza chiamata ```wait()```del parent
	- non viene ripulito spazio di memoria del processo (rimane nella process table)
- Processo **orfano**
	- child rimane in esecuzione mentre il parent è terminato
	- viene assegnato come child del processo iniziale (systemd in linux)
### comunicazione tra processi (ipc)
- S.O. permette comunicazione tra processi
- 2 modalità
![[Screenshot 2025-03-07 alle 11.35.22.png|300]]
### shared memory
- S.O. assegna parte **memoria** in **comune** tra più processi
	- fornisce i tool per inizializzare area di memoria condivisa
	- la responsabilità sull'utilizzo della memoria è a carico dei processi
- **Producer-consumer problem**
	- possibile **interferenza** tra processi
		- lettura della memoria da un processo mentre l'altro sta ancora scrivendo
	- bisogna garantire l'**esclusività** di utilizzo della memoria
- Vantaggi
	- più **veloce**:
		- non ha bisogno di passare tramite Kernel
	- più **flessibile**:
		- posso decidere in modo in cui i dati vengono trasferiti
	- permette di trasferire **grosse quantità di dati**
#### buffering
- **Unbounded-buffer**
	- memoria condivisa senza limiti
- **Bounded-buffer**
	- memoria condivisa di dimensione finita
	- producer deve aspettare se il buffer è pieno
### message passing
- S.O. fornisce system calls per inviare e ricevere messaggi
	- ```send(message)```
	- ```receive(message)```
- Operazioni dal punto di vista logico del message passing:
	- stabilire **communication link** tra i processi 
	- stabilire **tipo** di comunicazione
		- es. diretto/indiretto, sincroni/asincroni
- Vantaggi:
	- più **facile** da utilizzare
	- più **sicuro**
		- poiché gestito interamente da S.O.
	- si usa per dati di **piccola** dimensione
#### comunicazione diretta e indiretta
- Comunicazione **diretta**:
	- solo tra 2 processi
	- processo che invia/riceve specificati in modo esplicito
	- system calls tra processo ```P```a ```Q```
		- ```send(P, message)```
		- ```receive(Q, message)```
- Comunicazione **indiretta**
	- verso gruppi di processi
	- messaggi inviati e ricevuti da ==mailboxes== 
	- **mailbox sharing**
		- più processi possono utilizzare la stessa mailbox
		- ogni messaggio inviato è ricevuto da un singolo processo in base a politiche stabilite da S.O.
	- system calls con mailbox ```A```
		- ```send(A, message)```
		- ```receive(A, message)```
#### comunicazione sincrona/asincrona
- **Non-blocking**
	- codice prosegue nella sua esecuzione senza rimanere in attesa
- **Blocking**
	- invio
		- producer rimane in attesa finché il messaggio non viene ricevuto
	- ricezione
		- consumer rimane in attesa finché non riceve messaggio
![[Pasted image 20250307115356.png]]

<div style="page-break-after: always;"></div>

#### buffering
- **Zero capacity**
	- nessun messaggio in coda
	- il producer aspetta la ricezione del messaggio
	- permette di effettuare sincronizzazione tra processi (**rendezvous**)
- **Bounded capacity**
	- lunghezza buffer finita
	- producer aspetta se buffer è pieno
- **Unbounded capacity**
	- lunghezza buffer indefinita
	- nessun tempo di attesa nell'invio
### Pipes (tubi)
- Meccanismi per creare **comunicazioni** tra **processi**
#### ordinary pipes (o anonymous pipes)
- Utilizzate per **comunicazione** tra processo **parent**/**child**
- **Non** accessibili all'**esterno** del processo che le ha create
	- risiede all'interno del processo che le utilizza
- Comunicazione **unidirezionale**
	- presenza di **write-end** e **read-end**
	- per bidirezionale: basta creare due pipes
#### named pipes (o fifo)
- Viene assegnato un **nome** alla pipe
- Utilizzata per diversi **processi esterni** l'uno all'altro
- Avviene attraverso **file temporaneo** creato dal S.O
	- la pipe non si cancella alla terminazione di un processo che la usa
- Comunicazione **bidirezionale**