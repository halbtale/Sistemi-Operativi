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