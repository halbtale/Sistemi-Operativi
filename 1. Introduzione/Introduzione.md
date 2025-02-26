## introduzione
### sistema operativo
- **Software** che agisce da **intermediario** tra **utente** e l'**hardware** del computer
- **Agevola** uso computer da parte dell'utente
- Permette uso hardware in modo **efficiente**
	- agisce da ==allocatore di risorse== 
- Crea **interfaccia** in **comune** tra diverse architetture hardware
- **General purpose OS**
	- nato per applicazioni militari, si sviluppa poi per applicazioni civili
	- non si utilizza solo per i personal computer, ma anche su dispositivi mobili, dispositivi embedded, sistemi industriali, ecc
### struttura del sistema computer
- ==Hardware==
	- risorse computazionali (CPU, I/O, memoria, dispositivi, bus)
- ==Sistema operativo==
	- controlla e coordina utilizzo hardware
- ==Applicazioni==
	- utilizzate da utente o altre macchine
![[Pasted image 20250225131023.png|300]]

### componenti sistema operativo
- ==Kernel== (nocciolo)
	- componente **sempre** **attiva** quando il computer è accesso
- ==System program==
	- programmi accessori che permettono il buon funzionamento del sistema
	- forniti con il S.O.
	- si avviano dopo il Kernel
	- es. gestione di rete, grafica, multimedia, ecc
- ==Application program==
	- programmi non associati direttamente con il sistema operativo
![[Pasted image 20250225131651.png|400]]
### storia dei sistemi operativi
- **Sistemi a valvole e a programmazione manuale tramite interruttori**
	- si programmavano connettendo cavi
- **Sistemi batch con transistor e programmi scritti su schede perforate**
	- cartelline in cui venivano fatti dei fori in punti specifici
	- la sequenza dei punti veniva letta per poi essere eseguita come programmi
	- operazioni I/O molto lente
- **Multiprogrammazione con circuiti integrati**
	- eseguire più processi (job) in contemporanea
	- mentre uno è in attesa per I/O, l'altro esegue
- **Personal computers** e **sistemi operativi mobile**
### architettura di un computer
- **Operazione del sistema**
	- CPU, controller dispositivi, memoria condivisa connessi con bus comune
- **Esecuzione concorrente**
	- CPU, dispositivi I/O operano contemporaneamente
	- competono per accesso in memoia
- **Controller dispositivi**
	- ogni controller comunica con uno specifico dispositivo 
	- comunica con la CPU tramite interrupt segnalando completamento operazioni
![[Pasted image 20250225134134.png|500]]
#### gestione a interrupt s.o.
- ==Interrupt== = segnale che interrompe esecuzione CPU per gestire richieste ad alta priorità
- **Servizio degli interrupt**
	- controllo passa ad una routine specifica tramite tabella indirizzi (interrupt vector)
- **Salvataggio dello stato**
	- viene salvato indirizzo istruzione interrotta per poi riprenderla in seguito
- Tipi di interrupt
	- **hardware**
		- generati dai dispositivi I/O
	- **software**
		- causati da eccezioni o richieste al sistema operativo (system call)
### panoramica sistema operativo
![[Pasted image 20250225140051.png|600]]
### process management
- ==Processo==
	- programma in esecuzione
- Attività S.O.
	- **creazione**, **esecuzione**, **terminazione** processi 
	- **sospensione**/ripresa processi
	- **sincronizzazione** tra processi per evitare conflitti per accesso alle risorse
	- **comunicazione** tra processi per permettere scambio di dati
	- gestione ==deadlock==
		- più processi bloccati in attesa di risorse
	- in presenza di processori multi-core, più processi possono essere eseguiti in parallelo
#### multiprogramming (batch system)
- ==Job scheduling==
	- seleziona job da eseguire tra quell in memoria
- Quando un job è in **attesa** (operazioni I/O), esegue un altro job
	- CPU sempre occupata eseguendo **più job** in memoria
- Massimizza utilizzo risorse disponibili
#### multitasking (time sharing)
- Permette di eseguire **più programmi contemporaneamente**
	- Anche in computer single-core
- **Cambio rapido** tra **job**
	- CPU passa frequentemente da un processo all'altro
	- crea illusione che programmi vengano eseguiti contemporaneamente
- **Gestione processi**
	- se ci sono più processi pronti per essere eseguiti, si utilizza meccanismo CPU scheduling
- **Gestione RAM**
	- se memoria non è sufficiente, si usa **swapping**
	- memoria virtuale permette esecuzione di processi caricati solo parzialmente su RAM
#### dual-mode operation
- **Scopo**: sicurezza
- Due modalità di esecuzione
	- **User mode**
		- esecuzione applicazioni
		- limitazioni nelle operazioni consentite
	- **Kernel mode**
		- esecuzione codice sistema operativo
		- modalità esecuzione privilegiata: accesso completo all'hardware
- Gestione delle modalità
	- ==mode bit== (fornito dall'hardware)
		- 0 = kernel mode
		- 1 = user mode
	- **system call**
		- passa a kernel mode tramite chiamata a sistema
		- al termine dell'operazione ritorna a modalità utente
![[Pasted image 20250225140016.png|500]]
### memory management
- **Controllo** e **allocazione** memoria **processi**
	- CPU ha solo accesso a memoria
	- istruzioni e dati devono essere in memoria per poter essere eseguite
- Attività S.O.
	- traccia quali parti della memoria sono utilizzate e da chi
	- allocare/deallocare memoria ai processi in esecuzione
	- decidere quali processi caricare in memoria e quando
### file management
- Gestione **archiviazione**, **memorizzazione**, **accesso** dati memorizzati su memoria secondaria
- **Astrazione** fornita da S.O.
	- vista **logica** unificata
		- dettagli dispositivi nascosti
	- organizza dati in **file** e **directory**
		- gestione creazione, eliminazione, manipolazione
		- metadati associati: nome, dimensione, permessi modifica
		- mappatura file su memoria secondaria
	- **sicurezza** e **protezione**
		- controllo accessi e gestione permessi
#### storage structure
- **Organizzazione** **memoria** in **livelli gerarchici**
	- in base a velocità, costo, volatilità 
- Gerarchia memoria
	- **Registri CPU**
	- **Cache**
	- **Memoria principale** (RAM)
	- **Memoria secondaria** (disco, SSD)
	- **Memoria terziaria** (nastri magnetici)
#### mass-storage management
- Gestione **dispositivi** di **memorizzazione permanente** (dischi, SSD, memorie esterne)
- Attività S.O.
	- gestione spazio **libero**
	- **allocazione** dello spazio fisico
	- **scheduling** del disco
	- **partizionamento
	- **protezione** dei dati
### i/o control
- Gestione **operazioni** **input**/**ouput**
- Attività S.O.
	- fornisce **interfaccia generica** uso dispositivi I/O
	- gestione ==drivers== per diversi tipi di hardware
	- **buffering**
		- memorizzazione temporanea dati durante trasferimento
	- **caching**
		- conservazione su cache dei dati utilizzati più frequentemente
	- **spooling**
		- sovrapposizione operazioni I/O tra più processi
### protection and security
- **Protezione**
	- meccanismo di controllo accesso a risorse fornito da S.O.
- **Sicurezza**
	- protezione sistema da attacchi interni o esterni (virus)
- Gestione permessi utenti:
	- **User ID**
		- identità associata ad ogni utente
		- determina quali operazioni può effettuare l'utente
	- **Group ID**
		- controllo per un gruppo di utenti
### multiprocessor system
- Sistemi **multiprocessori** o **paralleli**
	- permettono più efficienza, velocità e tolleranza a errori
- **Asymmetric Multiprocessing**
	- a ogni processore è assegnata una specifica task
- **Symmetric Multiprocessing (SMP)**
	- processori utilizzano la stessa memoria
	- ogni processore può eseguire una qualsiasi task
	- a ogni processore può essere assegnata una propria cache e una cache in comune
- **Non-uniform memory access system (NUMA)**
	- architettura dove ogni processore:
		- può accedere velocemente a una propria memoria locale
		- può accedere alle altre memorie e alla memoria condivisa più lentamente
