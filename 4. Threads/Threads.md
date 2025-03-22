## threads
### introduzione
- Un ==thread== è un **filo di esecuzione** del processo
	- a cui è associato un **ID**, il **program counter**, un set di **registri** e uno **stack**
- Un processo può essere **multithreaded**
	- ad ogni processo è associato almeno un thread ma può averne più di uno
- Vantaggi:
	- **reattività** 
		- se una parte del processo è bloccata, non ferma l'esecuzione degli altri threads
		- es. GUI dell'applicazione è in un thread separato della logica interna
	- **condivisione di risorse**
		- i threads utilizzano la stessa memoria 
		- minore overhead rispetto a condivisione risorse tra processi
	- **economy**
		- thread switch è più veloce del context-switch (tra processi)
		- segue dal fatto che i thread hanno molte risorse in comune
	- **scalability**
		- permette di sfruttare architetture multicore
![[Pasted image 20250311124043.png|300]]
#### esempio: architettura server multithreaded
- Server crea un thread per ogni richiesta che riceve dal client
![[Pasted image 20250311124355.png|400]]
<div style="page-break-after: always;"></div>

### concorrenza e parallelismo
#### concorrenza
- Garantire il **progredire** di **più task** eseguendoli in **serie**
	- un singolo core esegue più task passando frequentemente da una all'altra
![[Pasted image 20250311125146.png|400]]
#### parallelismo
- **Architettura** che permette **esecuzione parallela** simultanea di **più task**
	- necessita di più core
- ==Data parallelism==
	- ogni core esegue le stesse task su un particolare **subset di dati**
- ==Task parallelism==
	- a ciascun core sono assegnate **task diverse**
![[Pasted image 20250311125242.png|250]]
>[!question] Cosa succede se ci sono più task che core disponibili?
>- S.O. esegue tante task in **parallelo** quanti i core e attua la tecnica di **concorrenza** per far progredire tutte le task in esecuzione 
>- Approccio **ibrido** tra concorrenza e parallelismo
### legge di amdahl
$$
\text{Speedup} \leq \frac{1}{S+\frac{(1-S)}{N}}
$$
- $S=$ percentuale porzione codice seriale (non parallelizzabile)
- $N=$ numero di core
#### codice parallelizzabile e seriale
- **Parallelizzabile**:
	- ogni iterazione è **indipendente**
- **Seriale**
	- ogni iterazione **dipende** da risultato di altre iterazioni
![[Pasted image 20250311125949.png]]
### classificazione threads
- ==User threads==
	- gestiti in **user mode** attraverso **librerie** dedicate
- ==Kernel threads==
	- gestiti in **kernel mode** all'interno del **sistema operativo**
>[!info] Nota
>Gli user threads devono essere mappati ai kernel threads i quali sono necessari, ad esempio, per gestire le relative system calls
### modelli multithreading
#### many-to-one
- **Più** **user threads** sono mappati ad un **unico** **kernel thread**
- Svantaggi:
	- non sfrutta pienamente capacità multicore poiché solo un thread può accedere al kernel alla volta
![[Pasted image 20250311131005.png|200]]

<div style="page-break-after: always;"></div>

#### one-to-one
- **Ogni** **user thread** è mappato ad un **kernel thread**
- Vantaggi:
	- sfrutta pienamente parallelismo sistema multicore
- Svantaggi:
	- si raddoppia il numero di thread che devono essere gestiti
- Usato su Windows e Linux
![[Pasted image 20250311131251.png|200]]
#### many-to-many
- **Più** user threads e kernel threads
	- **NO** corrispondenza 1-1
	- numeri di **kernel threads** creati dipendono dal **numero di core**
	- tutti i threads vengono gestiti da un unico collo di bottiglia
- Vantaggi:
	- permette di avere limitazioni minori sul numero di threads
- Svantaggi:
	- **NO** pieno parallelismo
	- collo di bottiglia causa maggiore overhead
![[Pasted image 20250311131515.png|200]]

>[!tip] Two-level model
>Approccio ibrido che ha una coppia mappata one-to-one e gli altri many-to-many

<div style="page-break-after: always;"></div>

### phreads (posix threads)
- **API** per la creazione dei threads e la sincronizzazione
	- contiene solo interfaccia
		- fa riferimento al **POSIX standard** 
	- implementazione fornita dal sistema operativo 
![[Pasted image 20250312113441.png|600]]
### threading implicito
- **Automatizzazione** **parallelizzazione** codice 
	- responsabilità data a compilatore o software dedicato
#### fork-join model
- **Libreria** decide come e quando creare più thread
- Strategia **divide and conquer**
	- task divisa in sottotask
	- per ogni task viene creato (```fork```) un **thread**
	- al termine delle operazioni, viene restituito il risultato (```join```) sul **thread** principale
![[Pasted image 20250311132908.png|500]]
#### thread pools
- **Libreria** crea un **set di threads vuoti** e li mantiene in attesa
- **Task** vengono **assegnati** ai **threads** nella pool
- Vantaggi:
	- **riduce** tempi di **attesa** (non deve creare/eliminare thread ogni volta)
	- permette di stabilire un **limite massimo** di threads eseguibili in parallelo
	- focus programmatore su **strategie** di esecuzione
		- no gestione a basso livello creazione/eliminazione threads
#### openmp
- Libreria C/C++, FORTRAN per programmazione multithreaded
- Si utilizzano delle **direttive** per indicare quali **parti** del **codice** **parallelizzare** in fase di compilazione
	- ```#pragma omp parallel```
#### altri esempi
- **Apple Grand Central Dispatch**
- **Intel Threading Building Blocks**
### note di attenzione sull'utilizzo dei threads
#### semantics
- ```fork```di un processo multithread
	- 2 opzioni:
		- duplica thread chiamante
		- duplica tutti i thread del processo chiamante
- ```exec``` in un processo multithread
	- rimpiazza tutti i thread
>[!tip] Suggerimento
>Quando faccio ```fork```+```exec```, è inutile duplicare tutti i thread dato che verranno rimpiazzati
#### signals
- Modalità per **notificare** a un processo che è avvenuto un particolare **evento**
	- gestita dal **S.O.**
- Processi possono gestire o ignorare segnali attraverso **signal handler**
	- default
	- user-defined
- Possibilità gestione segnali
	- segnali per uno specifico thread
	- segnali inviati a tutti i thread
	- thread dedicato per gestire i segnali
>[!tip] Comando ```kill```
>- Permette di inviare un segnale al processo
>	- ```SIGTERM```: per favore muori (default)
>	- ```SIGKILL```: ti uccido
#### thread cancellation
- **Asincrona**
	- termina **immediatamente** il thread target
- **Differita** (default)
	- il thread target **controlla** periodicamente se ha ricevuto richieste di cancellazione
	- solo ai **cancellation point**
		- punti in cui il thread può essere terminato in modo pulito
>[!info] Nota
>Un thread può **disabilitare** la possibilità di essere terminato
#### thread-local storage (tls)
- Spazio di memoria **dedicato** per un **singolo thread**
	- situato all'interno della memoria allocata per il processo
- Funzionamento simile a una variabile **statica** ma ce ne è **uno** **per thread**
#### scheduler activation
- **Schema di comunicazione** tra libreria user-thread e kernel
	- gestione mapping tra **kernel threads** e **user threads** in **many-to-many**
- **Lightweight process** (LWP)
	- struttura dati: set di processori virtuali
	- layer intermedio che lega un user thread ad un kernel thread 

<div style="page-break-after: always;"></div>

### implementazione threads nei vari sistemi operativi
#### windows threads
- Gestiti tramite **Windows API**
- Utilizza mapping **one-to-one**
- Ad ogni thread è associata una struttura dati contenente:
	- id
	- set di registri
	- user e kernel stacks
	- data storage privato
#### linux threads
- Gestiti tramite **POSIX API**
- **NO** distinzione esplicita **processi** e **threads**
	- chiamati genericamente ```task```
- Se una applicazione è multithreaded eseguirà un insieme di ```task```
- ```clone()```
	- system call che permette di creare un thread
	- accetta delle opzioni
		- ```CLONE_FS``` (condivisione file-system)
		- ```CLONE_VM``` (condivisione virtual-memory)
		- ```CLONE_SIGHAND``` (condivisione signal handler)
		- ```CLONE_FILES``` (condivisione set di file aperti)
	- se nessuna risorsa viene condivisa, funzionamento equivalente a ```fork```