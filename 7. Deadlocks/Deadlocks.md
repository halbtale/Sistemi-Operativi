## deadlocks
### modello di sistema
- Set di **risorse** 
	- $R_1,R_2,\dots\,R_m$
		- $R_i$ può includere una o più istanze della stessa risorsa
	- es. cicli CPU, memoria, dispositivi I/O
- Processo utilizzo risorsa:
	- **request**
	- **use**
	- **release**
### livelock
- Situazione in cui i **thread** non riescono ad avanzare poiché, pur non essendo bloccati, continuano a **provare** ad eseguire un'azione **senza successo**
- Esempio: thread in **non-blocking wait**
	- trova risorsa sempre **occupata**
	- non è bloccato ma **non termina** mai la propria task
### condizioni necessarie per deadlock
- ==Mutual exclusion==
	- almeno una risorsa acquisita in maniera **esclusiva** da un thread
- ==Hold and wait==
	- thread in **attesa** di una **risorsa** usata da thread
- ==No preemption==
	- le risorse **rimangono** al thread finché quello non completa la task
- ==Circular wait==
	- situazione di **attesa** di risorse **circolare**
### grafo di allocazione risorse
- Permette di rappresentare **stato** corrente del sistema
- $G=(V,E)$ 
- $V$: vertici partizionati in
	- **threads**
		- $T=\{T_1,\dots,T_n\}$
	- **classi di risorse**
		- $R=\{R_1,\dots,R_m\}$ 
- $E:$ archi partizionati in
	- ==request edge==
		- $T_i\rightarrow R_j$
		- thread **richiede** risorsa
	- ==assignment edge==
		- $R_j\rightarrow T_i$
		- istanza risorsa **acquisita** da thread
#### rilevamento deadlock
- **NO** clicli $\rightarrow$ **NO** deadlock
- **SI** cliclo 
	- **una sola** instanza per risorsa $\rightarrow$ **condizione necessaria e sufficiente** deadlock
	- **più** istanze per risorsa $\rightarrow$ **condizione necessaria** ma non sufficiente deadlock
#### stato con deadlock
![[Pasted image 20250401130050.png|150]]
- $R_2$, richiesta da $T_3$, non può essere rilasciata perché $T_1$ e $T_2$ non possono procedere se $T_3$ non rilascia risorse
#### stato senza deadlock
![[Pasted image 20250401130153.png|150]]
- Anche se è presente un ciclo, non appena $T_2$ rilascia $R_1$, potrà essere acquisita da $T_1$ rompendo il ciclo
### strategie gestione deadlock
- Evitare **preventivamente** di ottenere un deadlock
	- **deadlock prevention**
	- **deadlock avoidance**
- Permettere deadlock e **risanare** stato sistema se avvengono 
	- **deadlock detection** e **recovery**
- **Accettare** rischio deadlock 
	- soluzione adottata da sistemi operativi dove rischio deadlock è basso
	- soluzione più economica
### deadlock prevention
- **Invalidare** una delle condizioni necessarie per il deadlock
- ~~Mutual exclusion~~
	- utilizzare solo **risorse condivisibili** (es. file in lettura)
	- non sempre applicabile
- ~~Hold and wait~~
	- processo esegue solo dopo aver acquisito **tutte** le **risorse** necessarie per l'esecuzione
	- basso utilizzo CPU e possibile starvation
- ~~No Preemption~~
	- processo è in **attesa** di una risorsa occupata $\rightarrow$ **rilascia** risorse acquisite
	- inefficiente e richiede frequenti riavvii di thread
- ~~Circular wait~~
	- **vincolare** utilizzo risorse secondo **ordinamento** prefissato
		- posso richiedere $R_j$ dopo $R_i$ solo se $j>i$
	- difficile da gestire
### deadlock avoidance
- Strategie più efficienti per **evitare deadlock** che richiedono **informazione aggiuntive**
	- **numero massimo** di risorse richieste dal singolo processo
	- **stato** di allocazione risorse del singolo processo
- ==Safe state==
	- $\exists$ **sequenza** di esecuzione dei thread tale che **tutti** possono essere **completati**
		- al **caso peggiore** (per completare ogni thread richiede massimo uso risorse)
	- **NO** possibilità di verificarsi deadlock
	- si contrappone a **unsafe state**
		- può portare a deadlock
- **Algoritmi** cercano di mantenere l'esecuzione in uno **safe state**
	- due categorie: risorse singole o multiple
### resource-allocation graph algorithm
- Gestione di **istanze singole** di risorse per deadlock avoidance
- **Claim edge**
	- thread potrebbe **richiedere risorsa** in **futuro**
	- rappresentato con **linea tratteggiata**
	- informazione **a priori**
- **Passaggi di stato**
	- claim edge $\rightarrow$ request edge $\rightarrow$ assignment edge
- Algoritmo verifica se **assegnazione** di una risorsa genera **ciclo** (considerando claim edge)
	- in quel caso si avrebbe **unsafe state** $\rightarrow$ può portare a **deadlock**
	- **permette** solo assegnazioni che mantengono **safe state**
![[Pasted image 20250401132607.png|500]]
### algoritmo del banchiere
- Gestione di **istanze multiple** di risorse per deadlock avoidance
#### strutture dati algoritmo
- Vettore **available**
	- ```available[j]```: numero istanze disponibili per risorsa $R_j$
- Matrice **max**
	- ```max[i,j]```: numero istanze risorsa $R_j$ che thread $T_i$ può richiedere al massimo
- Matrice **allocation**
	- ```allocation[i,j]```: numero istanze risorsa $R_j$ che thread $T_i$ ha attualmente acquisito
- Matrice **need**
	- ```need[i,j]```: numero istanze risorsa $R_j$ che thread $T_i$ potrebbe acquisire in futuro
	- ```need = max - allocation```

![[Pasted image 20250401133715.png|300]]
![[Pasted image 20250401133901.png|600]]

#### safety check
- Fa una simulazione per vedere se sistema è in safe state
```
n = # threads
m = # resources

// vettore con risorse disponibili nella simulazione
int[m] work = available;

// vettore che indica quali thread hanno terminato
boolean[n] finish = [forall i, finish[i] = false];

// esegui procedimento n volte
for (int j = 0; j<n; j++) {

	// controlla se c'è un thread che posso eseguire
	for (int i = 0; i<n; i++) {
	
		// se thread è in esecuzione e ci sono abbastanza risorse disponibili
		if (finish[i] == false && need[i]<=work) {
		
			// termina processo e libera risorse allocate
			work = work + allocation[i]	
			finish[i] = true
	
			break;
		}
	}
}

if (forall i, finish[i] == true) {
	return SAFE
} else {
	return UNSAFE
}

```
#### scelta allocazione risorse
- Thread **richiede** $k$ istanze di una risorsa $R_j$
- Algoritmo **verifica** se risorse richieste < risorse disponibili
- Algoritmo effettua **safety check**:
	- **safe**: procede con l'assegnazione
	- **unsafe**:  non soddisfa assegnazione e valuta alternativa

### deadlock detection
- Algoritmi che **controllano** periodicamente se si è in uno stato di deadlock
- **Frequenza** controlli deadlock
	- in base alla **probabilità** in cui può verificarsi
	- $+$ tempo attesa $\Rightarrow$ $+$ dimensione deadlock
#### risorse a singola istanza
- **Wait-for graph**
	- semplificazione grafo allocazione risorse mantenendo solo i threads e le loro dipendenze
- Algoritmo controlla periodicamente per la presenza di **cicli** $\rightarrow$ **deadlock**
![[Pasted image 20250401135344.png|300]]
#### risorse a istanze multiple
- Modifica dell'algoritmo del **banchiere** per verificare se si trova in stato di deadlock
	- utilizza istanze risorse **richieste** invece che **massime** nel calcolo
### recovery from deadlock
#### process termination
- **Abort all**
	- termino tutti i thread affetti da deadlock
- **Abort one**
	- assegno **priorità** a thread e eseguo algoritmo per **scegliere** quale terminare
#### resource preemption
- **Rimozione** **risorse** **allocate** per alcuni thread affetti da **deadlock**
- Operazioni e problematiche **algoritmo**
	- scelta della **vittima**
		- sulla base di quante risorse deadlocked mantiene e quanto tempo è rimasto in esecuzione
	- **rollback**
		- thread a cui viene rimossa una risorsa dovrà essere riavviato
	- **starvation**
		- sempre gli stessi thread potrebbero essere scelti come vittime e quindi non riuscire mai a terminare
