## cpu scheduling
### cpu-i/o burst cycle
- **Multiprogrammazione**
	- obiettivo di sfruttare utilizzo massimo CPU eseguendo più processi in modo concorrente
- Processo costituito da **cicli**:
	- **CPU burst**
		- tempo dedicato per computazione
		- se si indica solo "burst" si intende questo
	- **I/O burst**
		- tempo dedicato per operazioni I/O
![[Pasted image 20250314105113.png|200]]
![[Pasted image 20250314105403.png|250]]
### cpu scheduler
- **Componente** che **seleziona**, dalla **ready queue**, processo da eseguire nella CPU
	- attraverso algoritmi di scheduling
- CPU-scheduling avviene quando un processo cambia stato
#### preemptive  e non-preemptive scheduling
- ==Non-preemptive scheduling==
	- processo **rimane in esecuzione** finché non termina o non passa al waiting state
- ==Preemptive scheduling==
	-  può **rimuovere forzatamente** un processo dalla CPU per assegnarne un altro
		- es. dopo un tempo massimo
	- tecnica usata dalla maggior parte dei sistemi operativi
	- **race conditions** in presenza di risorse condivise tra più processi
		- se rimuovo forzatamente un processo dalla cpu, caricare un nuovo processo può interferire con ciò che stava facendo il processo precedente
### dispatcher
- **Modulo** che **carica** il processo scelto dalla ready queue nella CPU
- Esegue una serie di operazioni:
	- **context switch**
	- switch a **user mode**
	- fa riprendere esecuzione programma da indirizzo di memoria corretto
- Impiega del tempo detto **dispatch latency**
![[Pasted image 20250314110426.png|150]]
### criteri di scheduling
- Massimizzare
	- **CPU utilization**
	- **throughput** 
		- numero di processi completati per unità di tempo
- Minimizzare
	- **turnaround time
		- tempo totale per eseguire un determinato processo contando le interruzioni
	- **waiting time**
		- tempo che un processo deve aspettare in totale nella ready queue
	- **response time**
		- tempo che un processo deve aspettare nella ready queue la prima volta
### first-come, first-served scheduling (fcfs)
- Ready queue si comporta come **coda FIFO**
	- il primo processo che arriva è il primo a essere servito
- Algoritmo di tipo **non-preemptive**
- Problemi:
	- ==convoy effect==
		- tanti processi brevi potrebbero rimanere in attesa esecuzione processo lungo
	- **varianza** molto alta dell'**average waiting time** 
		- è **fortemente influenzato** dall'ordine in cui i processi arrivano nella ready queue
![[Pasted image 20250314111432.png|500]]
>[!info] **GANTT Chart**
>- permette di rappresentare esecuzione temporale di più task
>- mostra ordine in cui processi vengono eseguiti
### shortest-job-first scheduling (sjf)
- Seleziona sempre il processo con il **burst time** più **piccolo**
- Algoritmo di tipo **non-preemptive**
- **Ottimo**
	-  offre il minore **average waiting time**
![[Pasted image 20250314112545.png|500]]
#### stima del burst time
- Non è sempre noto a priori il **burst time** di un processo
- Si applica **media esponenziale** del tempo di esecuzione passato del processo
- $\tau_{n+1}=\alpha t_{n}+(1-\alpha)\tau_n$ 
	- $t=$ valore **reale** per un dato intervallo
	- $\tau=$ valore **predetto** per un dato intervallo
	- $\alpha=$ **parametro** compreso tra 0 e 1
- Espandendo la formula ricorsiva si ottiene:
	- $\tau_{n+1}=\alpha t_{n}+(1-\alpha)\tau_n+(1-\alpha)^2\tau_{n-1}+(1-\alpha)^3\tau_{n-2}+\dots$ 
		- le predizioni passate pesano meno e meno nella media
	- $\tau_{0}$ potrebbe essere una costante o la media del sistema
- Casi **limite**:
	- $\alpha=0 \Rightarrow \tau_{n+1}=\tau_{n}$
		- usa sempre lo stesso valore di predizione (esecuzioni passate non contano)
	- $\alpha=1 \Rightarrow \tau_{n+1}=t_{n}$
		- usa l'ultimo valore reale
>[!tip] Valore di $\alpha$
>- Tipicamente si usa $\alpha=0.5$
>	- $\tau_{n+1}=\frac{t_n+\tau_n}{2}$

### shortest remaining time first scheduling (srtf)
- Versione **preemptive** di **SJF**
	- se **burst time** nuovo processo in ready queue $<$ **remaining time** processo attuale
		- interrompe processo attuale e esegue nuovo processo
![[Pasted image 20250314114121.png|500]]

>[!summary] Confronto tra algoritmi
>SRTF ha **average waiting time** inferiore a SJF

### round robin (rr)
- Versione **preemptive** di **FCFS**
- ==Time quantum== $q$
	- **unità** di **tempo** CPU
- Ad ogni processo viene **allocato** al **massimo** $1q$ di tempo di esecuzione alla volta
	- se non è terminato entro quell'intervallo, viene **interrotto** e rimesso in **coda**
- Garantisce un basso **response time**
	- con $n$ processi, ciascuno aspetterà al massimo $(n-1) q$ per essere eseguito
- La lunghezza del quanto deve essere grande rispetto al tempo per **context switch**
	- altrimenti overhead rende algoritmo inefficiente
	- $q\in [10,100]\,ms$
	- context switch $<$ $10ms$
![[Pasted image 20250314115417.png|500]]
>[!summary] Confronto tra algoritmi
>RR ha **average turnaround** maggiore di SJF ma ha un **response time** minore

### priority scheduling
- Esegue **prima** processi a **priorità maggiore**
	- numero più piccolo = priorità più alta
- Generalizzazione di SJF
	- priorità inversa del tempo di CPU burst 
- Può essere sia **non-preemptive** che **preemptive**
- Problema: **starvation**
	- processi a bassa priorità potrebbero non venir mai eseguiti
	- si sfrutta tecnica ==aging==
		- **priorità** processi in attesa **incrementata gradualmente**
#### priority scheduling con round-robin
- Esegue prima processi con **priorità più alta**
- Processi con **pari priorità** eseguiti con politica **round-robin**
### multilevel queue scheduling
- **Ready queue** costituita da code **multiple**
- Definita dai parametri:
	- **numero** di code
	- algoritmo di **scheduling** per ciascuna coda e tra le code
	- metodo per determinare **a quale coda** viene assegnato un processo

<div style="page-break-after: always;"></div>

#### priority scheduling con multilevel queue
- Una **coda** per **ogni** **priorità**
	- ad ogni processo è assegnata sempre una **specifica** coda
- **Scheduling** in ogni coda scelto in base al tipo di processo, ad esempio:
	- coda foreground: RR (response time minimo)
	- coda background: FCFS (presenza di processi con burst time maggiore)
- **Scheduling** tra le code, si può:
	- dare priorità assoluta **preemptive** a code con priorità maggiore
	- assegnare ad ogni coda una **percentuale** di tempo di utilizzo della CPU
![[Pasted image 20250314175148.png|400]]
### multilevel feedback queue scheduling (mlfq)
- Permette a un processo di **cambiare coda**
- **Algoritmi** che determinano quando processo può fare **upgrade**/**downgrade** di coda
	- permette di dare **priorità** a processi con CPU **burst brevi**
	- permette meccanismo di **aging** per evitare **starvation**
#### esempio
- 3 code in ordine di **priorità**
	- $Q_0$: **RR** con $q=8ms$
	- $Q_1$: **RR** con $q=16ms$
	- $Q_2:$ **FCFS**
- Tutti i processi entrano per la prima volta in $Q_{0}$
	- se non termina in tempo, viene spostato a $Q_{1}$
	- permette di dare priorità massima a processi veloci (I/O-bound)
- Se $Q_{0}$ è vuota, vengono eseguiti i processi in $Q_{1}$
	- se non terminano in tempo, vengono spostati in $Q_{2}$
- Se $Q_{0}$ e $Q_{1}$ sono vuote, vengono eseguiti processi in $Q_{2}$
	- i processi più lunghi ottengono priorità più bassa
	- per evitare **starvation**, i processi che rimangono in attesa troppo vengono spostati gradualmente in code a priorità più alta
![[Pasted image 20250314175921.png|250]]


<div style="page-break-after: always;"></div>

### real-time cpu scheduling
- Alcuni processi hanno bisogno di tempi di risposta quasi istantanea
	- es. ABS, automazione industriale, difesa missilistica, real-time operating system
- ==Event latency==
	- intervallo di tempo che intercorre tra evento e risposta del sistema
	- **interrupt latency**
		- ritardo tra ricezione interrupt e attivazione routine per gestirlo
	- **dispatch latency**
		- ritardo dovuto allo scambio del processo running effettuato dal dispatcher
### classificazione di real-time scheduling
- ==Soft real-time system==
	- esegue in modo **preemptive** le task a **priorità** più **alta**
- ==Hard real-time system==
	- offre **garanzia** che processo verrà completato entro una data **deadline**
	- **admission control**
		- può **accettare** o **rifiutare** processi in base a se riuscirà a completarli entro **deadline**
### processi periodici
- Processi eseguiti periodicamente:
	- $p$: **periodo** ogni quanto deve essere eseguito
		- $rate=\frac{1}{p}$
	- $t$: **durata** esecuzione
	- $d$: **deadline** entro cui deve essere eseguito
		- tipicamente $d=p$
![[Pasted image 20250318130913.png|450]]

<div style="page-break-after: always;"></div>

### rate-monotonic scheduling
- **Priorità** = **rate** processo ($\frac{1}{p}$)
- ==Worst-case cpu utilization==
	- $N(2^{\frac{1}{N}}-1)$
		- $N=$ numero processi
		- con $N\rightarrow \infty$ tende a $69\%$
	- **utilizzo massimo** della **CPU** con rate-monotonic scheduling al **caso peggiore**
		- se utilizzo richiesto è superiore, rate-monotonic potrebbe non riuscire a soddisfare tutte le deadline
#### esempio 
- Due **processi**:
	- $P_1$: $(p_1, t_1)=(50,20)$
	- $P_2$: $(p_2, t_2)=(100,35)$ 
- **Analisi** utilizzo CPU:
	- $P_{1}: \frac{20}{50}=40\%$
	- $P_{2}: \frac{35}{100}=35\%$
	- Totale: $75\%$
- **Priority-based preemptive scheduling**
	- $P_2$ prioritario (arbitrario)
	- **fallisce**: non riesce a soddisfare deadline $P_1$
	- adatto solo a **soft real-time system**
![[Pasted image 20250318131346.png|400]]
- **Rate-monotonic scheduling**
	- $P_1$ prioritario (frequenza esecuzione più alta)
	- ha **successo**
![[Pasted image 20250318131818.png|500]]
### earliest deadline first scheduling (edf)
- **Priorità = deadline**
	- più vicina è la deadline, più alta la priorità
	- funziona anche per processi non periodici
- Algoritmo teoricamente **ottimale**
	- utilizzo CPU tende a $100\%$
#### esempio
- Due **processi**:
	- $P_1$: $(p_1, t_1)=(50,25)$
	- $P_2$: $(p_2, t_2)=(80,35)$ 
- **Analisi** utilizzo CPU:
	- $P_{1}: \frac{25}{50}=50\%$
	- $P_{2}: \frac{35}{80}=44\%$
	- totale: $94\%$
- **Rate-monotonic scheduling**
	- $P_1$ prioritario (frequenza esecuzione più alta)
	- **fallisce**: $P_2$ non riesce a completare entro la sua deadline
		- CPU utilization superiore a _worst-case CPU utilization_ ($84\%$)
![[Pasted image 20250318132300.png|500]]
- **EDF scheduling**
	- al tempo 50, la deadline di $P_2$ è più vicina di quella di $P_1$
		- viene eseguito $P_2$ 
		- idem negli altri casi
	- ha **successo**
![[Pasted image 20250318133122.png|500]]
### proportional share scheduling
- $T=$ ==shares==
	- unità di tempo di CPU totali disponibili
- A ogni processo viene assegnato un certo numero $N$ di share
	- corrispondente **allocazione** $\frac{N}{T}$ **tempo** utilizzo **CPU**
- **Admission-control policy**
	- processo viene **accettato** se le $N$ **share** richieste sono disponibili

<div style="page-break-after: always;"></div>

### multi-processor scheduling
- **Scheduling** processi deve sfruttare architetture sistemi **multiprocessore**:
	- **multicore CPUs**
	- **multithreaded cores**
	- **NUMA systems**
	- **heterogeneous multiprocessing**
#### asymmetric multiprocessing
- Processori eseguono **task differenti**
	- un processore si occupa delle attività S.O. (scheduling, I/O, ecc)
	- gli altri processori si occupano dell'esecuzione del codice utente
- Possibile effetto **collo di bottiglia**
#### symmetric multiprocessing (smp)
- Approccio **standard** 
- Ogni processore è **self-scheduling**
- Modalità esecuzione task:
	- condivisione unica **ready queue** comune
	- una **ready queue** per ogni core (più usato)
![[Pasted image 20250318134316.png|300]]
### load balancing
- Scheduling deve **distribuire** in modo **uniforme** processi sulle diverse CPU 
- ==Push migration==
	- gestione a livello **supervisore**
	- **processo** dedicato che **bilancia periodicamente** distribuzione del carico
- ==Pull migration==
	- gestione a livello **individuale**
	- **CPU** idle **prende** task in attesa da CPU occupate
#### processor affinity
- Ogni processo sviluppa una **processor affinity**
	- affinità con CPU su cui sta eseguendo
	- **warm cache**: cache CPU contiene dati del processo eseguito
- Load balancing deve tenere conto di **processor affinity** per evitare perdita cache:
	- ==soft affinity==
		- sistema operativo cerca di **mantenere** processi nella **stessa CPU**
	- ==hard affinity==
		- si specifica **subset** di **CPU** dove un certo processo può essere eseguito
- Essenziale in architetture **NUMA**
	- ogni CPU ha la propria memoria
	- vi è una forte **processor affinity** dei processi con la CPU
	- load balancing deve essere **NUMA-aware**
![[Pasted image 20250318140211.png|400]]
### multithreaded multicore systems
- Dettagli architettura:
	- **multicore**: più core nello stesso chip della CPU
	- **multithreaded**: più thread fisici nello stesso core
- S.O. vede ogni **thread** fisico come **CPU logica distinta**
![[Pasted image 20250318134407.png|150]]
#### chip-multithreading (cmt)
- Ogni processo è costituito da:
	- **compute cycle**
		- tempo dedicato alla computazione
	- **memory stall cycle**
		- tempo dedicato all'accesso in memoria
		- tempo morto in cui processore rimane idle
- **Implementazione** in **hardware** di due **thread fisici** di esecuzione:
	- non appena c'è un memory stall in un thread, esegue l'altro thread
![[Pasted image 20250318134943.png|400]]
- **Scheduling di due livelli**
	- **hardware**
		- scheduling thread hardware nella stessa CPU fisica
	- **sistema operativo**
		- scheduling processi su CPU logiche
		- ottimizzato se a conoscenza dell'architettura hardware

### esempi di scheduling nei sistemi operativi
#### linux scheduling
- Utilizza ==Completely Fair Scheduling (CFS)==
- Supporta **load balancing** e **NUMA**
- Supporta **domini** per processor affinity
	- dominio = subset cpu su cui si può eseguire load balancing
#### windows scheduling
- Utilizza ==Priority-based Preemptive Scheduling==
- 32 **classi** di priorità
	- variabili o real-time
	- coda per ogni priorità
- Se non c'è nessun processo in esecuzione, CPU esegue idle thread