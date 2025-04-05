## sincronizzazione
### introduzione
- Processi **cooperativi**
	- eseguono in modo concorrente
	- possono accedere a risorse condivise
	- possono venir interrotti in qualsiasi momento prima di terminare esecuzione
		- può creare **inconsistenze** e **conflitti** sulle risorse
### race condition
- Situazione in cui più processi **manipolano** le **stesse risorse** condivise e il cui **output** dipende dall'**ordine** in cui avviene l'accesso a tali risorse
#### esempio 1
![[Pasted image 20250321105006.png|700]]
- **Counter**
	- numero di elementi del buffer
	- variabile condivisa tra producer e consumer
- **Producer**
	- produce nuovo messaggio
	- rimane in attesa finché buffer è pieno
	- inserisce dato nel buffer
	- incrementa _counter_ 
- **Consumer**
	- rimane in attesa finché buffer è vuoto
	- legge dato dal buffer
	- decrementa _counter_ 
- Analisi esecuzione **concorrente**:
	- possibile conflitto nell'accesso alla variabile _counter_
![[Pasted image 20250321105614.png|600]]
#### esempio 2
![[Pasted image 20250321110049.png|350]]
- **Kernel** ha variabile interna ```next_available_pid```
	- viene incrementata per ogni processo creato
- Due **processi paralleli** chiamano ```fork```
	- possibile **interruzione** nel mezzo dell'aggiornamento
	- i due child ottengono lo **stesso** ```pid``` $\rightarrow$ *bug*

<div style="page-break-after: always;"></div>

### critical-section problem
- ==Sezione critica==
	- **parte** di codice che deve essere eseguita in modo **esclusivo** in un **processo**
		- es: utilizza **risorse condivise**
	- se non gestita correttamente $\rightarrow$ **race conditions**
![[Pasted image 20250321110459.png|150]]
#### requisiti risoluzione problema
- ==Mutual exclusion==
	- **un processo alla volta** esegue *sezione critica*
- ==Progress==
	- attesa esecuzione *sezione critica* **NON** deve dipendere da processi che non sono **pronti** a eseguire la *sezione critica*
- ==Bounded waiting==
	- **limite** numero di processi che possono **precedere** quello in attesa per _sezione critica_
	- garantire tempo di attesa **massimo**
### soluzione critical-section problem con interrupt
- **Disabilitare** interrupt quando viene eseguita la sezione critica di un processo
- Viene utilizzata solo per alcune operazioni di kernel

>[!failure] Svantaggi
>- Un processo potrebbe rimanere nella sezione critica a lungo e bloccare tutti gli altri
>- Sistema multiprocessore: dovrei bloccare tutte CPU

<div style="page-break-after: always;"></div>

### soluzione critical-section problem con turni software
- Utilizzo **1 variabile**
	- ```int turn```
		- **turno** per eseguire sezione critica
![[Pasted image 20250321111351.png|600]]

>[!info] Requisiti soddisfatti
>- ✅ Mutual exclusion
>- ❌ Progress
>	- se $P_1$ avesse bisogno di eseguire sezione critica due volte di seguito, dovrebbe aspettare $P_0$ anche se **NON** è ancora pronto per eseguire sezione critica
>- ✅ Bounded waiting

### soluzione critical-section problem di peterson
- Utilizzo di **2 variabili**
	- ```int turn```
		- **turno** per eseguire sezione critica
	- ```boolean flag[2]```
		- indica se processo è **pronto** per eseguire sezione **critica**
- Un processo può **eseguire** parte critica sempre se l'altro non è **pronto** ad eseguire la propria
![[Pasted image 20250321112123.png|300]]

>[!info] Requisiti soddisfatti 
>- ✅ Mutual exclusion
>- ✅ Progress
>- ✅ Bounded waiting
#### problema del reordering
- **Ordine** delle istruzioni può venire **invertito** dal compilatore
	- es. inversione ordine assegnazione variabile condivisa e flag
- Può portare a **accesso** **contemporaneo** sezione critica processi 
![[Pasted image 20250321113018.png|600]]
#### soluzione della memory barrier
- Architetture possono permettere:
	- **strongly ordered**
		- modifiche memoria immediatamente visibili su tutti i processori
	- **weakly ordered**
		- modifiche memoria **NON** immediatamente visibili su tutti i processori
- ==Memory barrier==
	- istruzione che **forza** **propagazione** modifiche memoria su tutti i processori
	- si può usare per **garantire** che la variabili siano aggiornate nell'**ordine** corretto
		- evita problema reordering
>[!fail] Svantaggi
>Istruzione di basso livello tipicamente usata solo dagli sviluppatori kernel
### istruzione Hardware: test-and-set
![[Pasted image 20250321114003.png|400]]
- Istruzione **atomica** implementata in HW
	- **garantito** che non verrà interrotta a metà
- Funzionamento
	- manipolazione variabile ```target```passata come **puntatore**
	- imposta ```target```a ```true```
	- restituisce valore **originale** di ```target```

<div style="page-break-after: always;"></div>

#### soluzione al critical-section problem
![[Pasted image 20250321114228.png|250]]

- ```lock```
	- flag per **bloccare** accesso a sezione critica
- ```while```loop: esegue ```test_and_set```
	- blocca **codice** finché ```lock```è ```true```
	- non appena ```lock```diventa ```false```
		- ```lock```viene impostata immediatamente a ```true```
		- restituisce ```false``` e **esce** dal while

>[!info] Requisiti soddisfatti 
>- ✅ Mutual exclusion
>- ✅ Progress
>- ❌ Bounded waiting
>	- un processo molto veloce potrebbe acquisire sempre il lock

### istruzione compare-and-swap (Cas)
![[Pasted image 20250321114722.png|500]]
- Istruzione **atomica** implementata in HW
	- **garantito** che non verrà interrotta a metà
	- **generalizzazione** test-and-set
- Funzionamento
	- manipolazione variabile ```value```passata come **puntatore**
	- controlla se ```value```è uguale a ```expected```
		- se lo è, aggiorna ```value```con un nuovo valore (```new_value```)
	- restituisce valore **originale** di ```value``` memorizzato su variabile temporanea
#### soluzione al critical-section problem
![[Pasted image 20250321115016.png|300]]


- ```lock```
	- flag per **bloccare** accesso a sezione critica
- ```while```loop: esegue ```compare_and_swap```
	- blocca **codice** finché ```lock```è ```true```
	- non appena ```lock```diventa ```false``` **esce** dal ciclo e lo aggiorna nuovamente a ```true```

>[!info] Requisiti soddisfatti 
>- ✅ Mutual exclusion
>- ✅ Progress
>- ❌ Bounded waiting
>	- un processo molto veloce potrebbe acquisire sempre il lock 

### variabili atomiche
- **Modibicabili** in modo **atomico**
	- unica operazione HW che non può essere interrotta a metà
- Funzioni helper per modificarle sfruttano ```compare_and_swap```
#### esempio: funzione helper incremento
![[Pasted image 20250321115411.png|400]]
- Esecuzione ```while```
	- **dereferenzia** ```v```su ```temp```
	- se ```*v==temp``` 
		- **incrementa** valore di ```v``` (a ```temp+1```)
	- altrimenti ripete ciclo

>[!info] Osservazione
>Posso utilizzare questo metodo direttamente su variabili condivise senza **race conditions**
### mutex lock
- Variabile **atomica** booleana che indica se lucchetto è disponibile o meno
	- offerta dal **sistema operativo**
- Garantisce **mut**ual **ex**clusion
	- un processo alla volta acquisisce il lucchetto per accedere alla sezione critica
- Operazioni (tipicamente implementate con ```compare_and_swap```)
	- ```acquire()```
	- ```release()```
![[Pasted image 20250321120008.png|250]]
>[!failure] Svantaggio
>- **Busy waiting**: ciclo continuo che controlla se lock è disponibile (**spinlock**)
>- Possibile spreco di risorse computazionali
>- Soluzione valida se attesa è stimata breve

<div style="page-break-after: always;"></div>

### semaforo
- Variabile intera **S** che può essere modificata attraverso operazioni **atomiche**
- ```wait()``` o ```P()```: rimane in **attesa** se ```S<=0```e poi ==decrementa== 
![[Pasted image 20250328104656.png|150]]
- ```signal()``` o ```V()```: ==incrementa==
![[Pasted image 20250328104723.png|120]]
#### tipologie
- ==Counting semaphore==
	- può assumere qualunque valore **intero**
	- tipicamente viene inizializzato al numero di risorse disponibili
- ==Binary semaphore==
	- può assumere solo **0** e **1**
	- equivale a un **mutex lock**
#### risoluzione critical-section problem
![[Pasted image 20250328104929.png|200]]
- Se ```mutex==0```
	- rimango in attesa (busy waiting)
- Se ```mutex==1```
	- imposto ```mutex=0```e eseguo sezione critica
	- al termine della sezione critica imposto ```mutex=1```
#### risoluzione problemi sincronizzazione
- Definire **ordine** in cui processi accedono a sezione critica
	- es. accesso prima lettura e poi scrittura ad un file
![[Pasted image 20250328105300.png|200]]
- $P_2$ rimane in attesa finché $P_1$ non incrementa ```synch```
#### semafori senza busy waiting
![[Pasted image 20250328105946.png|200]]
- Semaforo è **struttura dati** con:
	- ```value```: **valore** semaforo
	- ```list```: puntatore alla **waiting queue**
		- necessaria per evitare **busy waiting** (spreco risorse computazionali)
- **Operazioni interne** semaforo:
	- ```sleep()```
		- sposta processo su **waiting queue**
	- ```wakeup()```
		- rimuove processo da waiting queue e lo sposta su **ready queue**

![[Pasted image 20250328110256.png|350]]
- Operazioni ```wait```
	- **decrementa** valore semaforo
	- se valore $<0$, significa che era al massimo a $0$ prima del decremento
		- non ci sono risorse disponibili
		- mette il processo in ```sleep```

![[Pasted image 20250328110322.png|350]]
- Operazioni ```signal```
	- **incrementa** valore semaforo
	- se valore $<=0$, significa che c'è almeno un processo nella waiting queue
		- dato che è stato chiamato signal, si è liberata una risorsa disponibile
		- prende processo da waiting queue e lo sposta nella ready queue per essere eseguito

>[!tip] Implementazione reale
>- Per ottenere le operazioni **atomiche** utilizza ```compare_and_swap```
>- Busy waiting trascurabile
>
>![[Pasted image 20250328110858.png]]

>[!bug] Usi scorretti semafori:
>- Chiamare ```signal```prima di ```wait```
>	- starei _rilasciando_ una risorsa che non ho acquisito
>- Chiamare due ```wait``` consecutivi
>	- processo si _blocca_ permanentemente alla seconda chiamata
>- Dimenticare ```signal```dopo ```wait```

<div style="page-break-after: always;"></div>

### monitor
- **ADT** che facilita la **sincronizzazione** tra processi
- Tipicamente si può associare a una **classe**
	- gli oggetti otterranno le caratteristiche offerte dal monitor
- Assicura **mutual exclusion**
	- tutti i metodi hanno un **mutex** come **wrapper**
	- **variabili** interne accessibili solo all'**interno** del monitor
	- solo un processo può eseguire codice nel monitor alla volta
		- gli altri processi vengono messi in coda
![[Pasted image 20250329084510.png|300]]
>[!info] In Java
>- Ad ogni oggetto è associato implicitamente un ```monitor```
>- Si può aggiungere la keyword ```synchronized``` su un metodo per attivare il wrapper del mutex lock
#### variabile condizione
- ```condition x```
	- ```x.wait()```
	- ```x.signal()```
- Offre più **garanzie** di un semaforo
	- se chiamo ```signal```e non c'è nessun ```wait```, non ha alcun effetto
- ==Conditional wait==
	- tipicamente ```wait```segue ```FCFS```
	- è possibile impostare una **priorità** nel risveglio
	- ```x.wait(c)```
		- $c=$ **priority number**
	- un **resource allocator** potrebbe utilizzare ```time```come priorità
#### esempio
- Implementazione di allocatore di risorse attraverso un monitor
![[Pasted image 20250329085828.png|150]]
>[!bug] Problematiche monitor
> Uso logico scorretto dei metodi offerti dal monitor

### liveness problem
- Problema: alcuni problemi potrebbero rimanere in waiting per un tempo **indefinito**
- ==Liveness==
	- insieme di **proprietà** che il sistema deve soddisfare per garantire **progresso** processi
#### deadlock
- Due o più processi rimangono in **attesa** per **tempo indefinito** per eventi che possono essere **causati** solo da uno dei **processi** in **attesa**
![[Pasted image 20250328113227.png|200]]
- Potrebbe succedere questo ordine di esecuzione:
	- $P_0$: ```wait(S)``` -> prende semaforo
	- $P_1$: ```wait(Q)``` -> prende semaforo
	- $P_0$: ```wait(Q)```-> bloccato 
	- $P_1$: ```wait(S)```-> bloccato 
#### starvation
- Processo a **bassa priorità** rimane in attesa per tempo **indefinito**
	- viene **sorpassato** da processi a priorità più alta
- Soluzione: **aging**
#### priority inversion
- Processo ad **alta priorità** nello scheduling richiede lock usato da processo a **bassa priorità**
	- se processo a bassa priorità non viene eseguito subito, rimane bloccato anche quello ad alta priorità
- Soluzione: **priority-inheritance protocol**
	- processi che utilizzano **lock** usati da processi ad alta priorità ereditano la alta priorità
### bounded-buffer problem
- **Producer-consumer** con accesso a un buffer di $n$ elementi
#### soluzione
- Si possono sfruttare 3 semafori:
	- ```mutex```: accesso in modo esclusivo al buffer
	- ```fully```: conta quante caselle del buffer sono piene
	- ```emtpy```: conta quante caselle del buffer sono vuote

![[Pasted image 20250328114205.png]]
- **Produttore**
	- aspetta ci siano caselle libere
	- acquisisce mutex, inserisce elemento, rilascia mutex
	- segnala inserimento elemento su buffer
- **Consumatore**
	- aspetta che ci sia almeno un dato nel buffer
	- acquisisce mutex, rimuove elemento, rilascia mutex
	- segnala rimozione elemento nel buffer
>[!warning] Attenzione
>- L'ordine delle istruzioni è importante, altrimenti si verifica ```deadlock```

### readers-writers problem
- **Dataset** condiviso da più processi concorrenti il cui accesso è permesso:
	- in **lettura**: più processi in simultanea
	- in **scrittura**: solo un processo alla volta
- Si assume che i lettori in attesa hanno la precedenza sugli scrittori in attesa
#### soluzione
- Variabile ```read_count```: conta quanti lettori stanno leggendo
- Si possono sfruttare 2 semafori:
	- ```rw_mutex```: accesso in modo esclusivo scrittura risorsa
		- inizializzato a **1**
	- ```mutex```: accesso per poter modificare ```read_count```
		- inizializzato a **1**

![[Pasted image 20250328115645.png]]
- **Scrittore**
	- acquisisce ```rq_mutex```
	- scrive sulla risorsa
	- rilascia ```rq_mutex```
- **Lettore**
	- ogni volta che deve modificare ```read_count```acquisisce ```mutex```
	- primo lettore acquisisce ```rq_mutex``` e ultimo lettore rilascia ```rq_mutex```

<div style="page-break-after: always;"></div>

### dining-philosophers problem
- 5 filosofi a cena su **tavolo circolare**
	- per mangiare devono acquisire **bacchetta** a **destra** e a **sinistra**
	- possono prendere una sola bacchetta alla volta
![[Pasted image 20250328120013.png|200]]
>[!danger] Rischio deadlock
>Se tutti i filosofi prendono la bacchetta destra, rimarranno in attesa in modo indefinito per quella a sinistra
#### soluzione
- Utilizzo di un **monitor**
	- garantisce **mutual exclusion** nelle operazioni effettuate dai metodi
	- permette utilizzo **condition**

![[Pasted image 20250329091335.png|300]]
![[Pasted image 20250329091352.png|300]]

- **Inizializzazione**
	- tutti i filosofi ricevono stato di _THINKING_
- **Pickup**
	- filosofo cambia stato in _HUNGRY_ 
	- controlla (```test```) se entrambe le bacchette sono disponibili
		- _disponibili_: cambia stato in _EATING_ 
		- _non disponibili_: usa ```wait()```sulla propria condition
- **Putdown**
	- filosofo cambia stato in _THINKING_
	- avvia controllo (```test```) su bacchette disponibili per i filosofi adiacenti
		- _disponibili_: cambia stato del relativo filosofo in _EATING_ e lo risveglia con ```signal```
		- _non disponibili_: nessuna azione

<div style="page-break-after: always;"></div>

### posix api per sincronizzazione
- Disponibile anche in **user mode**
- Sono forniti:
	- **mutex locks**
	- **semafori** 
		- anonimi o con nome
	- variabili **condizione**
	- **NO** monitor 
- Utilizzato in UNIX, Linux, MacOS