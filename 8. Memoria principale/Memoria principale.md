## memoria principale
### introduzione0
- La **CPU** può accedere direttamente solo a
	- **registri**
	- **memoria principale** (ram, cache)
- Unità di memoria vede solo **stream di byte**:
	- indirizzo + richiesta (lettura/scrittura)
	- nessuna distinzione tra codice e dati
- **RAM** è significativamente più **lenta** della cpu
	- accesso memoria dura diversi cicli macchina (**memory stall**)
		- tempo minimizzato da architetture moderne con più **thread fisici** nello stesso core
	- **cache** veloce utilizzata per evitare accesso ram per dati più frequenti
		- implementazione HW senza controllo S.O.
### protezione memoria base
- **Processi** devono risiedere in zone di memoria **esclusive**
- Per ogni processo vengono fissati 2 registri:
	- **base**: offset posizione inizio memoria allocata
	- **limit**: dimensione memoria allocata
- Sistema operativo **controlla** che indirizzi di memoria richiesti da un processo appartengano alla zona di memoria dedicata ad esso
![[Pasted image 20250404105519.png|400]]
### mappatura degli indirizzi
- Codice sorgente
	- indirizzi tipicamente simbolici (usando label)
- Compilatore
	- indirizzi simbolici tradotti in indirizzi relativi (relocatable addresses)
		- facilitano inserimento programma in zone di memoria diverse
- Linker/loader
	- converte indirizzi relativi in assoluti
#### modalità binding mappatura indirizzi di memoria assoluti
- Può avvenire in 3 momenti diversi:
	- **compile-time**
		- tipico sistemi embedded
	- **load-time**
		- genera codice rilocabile
	- **execution-time**
		- mappatura può avvenire durante l'esecuzione del processo
		- permette di spostare processo in locazioni di memoria diverse
### spazio degli indirizzi logico e fisico
- **Spazio indirizzamento logico**
	- indirizzo logico usato programma e dalla cpu
- **Spazio indirizzamento fisico**
	- indirizzo fisico usato dalla unità di memoria
- Permette di avere dimensione di spazi di indirizzamento differenti
#### memory-management unit (mmu)
- Componente hardware che permette di effettuare conversione da indirizzo logico a fisico
![[Pasted image 20250404110946.png|250]]
![[Pasted image 20250404110808.png|400]]
### dynamic loading
- Permessa dall'execution-time binding
- Permette di caricare in memoria solo una parte del programma ma dinamicamente solo la parte utilizzata
### organizzazione memoria
#### allocazione contigua
- 2 macrosezioni
	- una dedicata al sistema operativo
	- una per i processi
- Memoria viene riempita in modo contiguo
	- permette uso efficiente della memoria
	- utilizzato dai primi sistemi in presenza di risorse limitate
- Può essere sfruttato supporto hardware
![[Pasted image 20250404111346.png|100]]
#### fixed partition
- Memoria divisa una serie di partizioni prefissate di **dimensione variabile**
	- ogni partizione può contenere solo un processo
- È più facile da gestire
- Problema
	- la partizione più grande potrebbe non essere sufficiente
![[Pasted image 20250404111812.png|150]]
#### variable partition
- Quando un processo termina, lascia dello spazio libero che verrà sfruttato da altri processi
- In presenza di spazi liberi contigui, viene fatto un merge e creato uno spazio libero più grande
![[Pasted image 20250404111631.png|300]]
- Strategie per scegliere dove allocare un processo tra i vari memory hole grandi a sufficienza:
	- ==first-fit==
		- alloca nuovo processo nella prima zona di memoria libera
		- molto veloce
	- ==best-fit==
		- alloca nuovo processo nella zona di memoria libera più piccola
		- deve scansionare tutte le possibili zone di memoria libere
	- ==worst-fit==
		- alloca nuovo processo nella zona di memoria libera più grande
		- deve scansionare tutte le possibili zone di memoria libere
### frammentazione
- Spreco di spazio di memoria lasciando buchi troppo piccoli per essere usati da altri processi
#### frammentazione interna
- Tipica della fixed partition
- Se il processo è più piccolo della partizione, lo spazio rimanente è sprecato
#### frammentazione esterna
- Tipica della variable partition
- Area di memoria totale libera è sufficiente per nuovi processi ma, essendo frammentata, non può essere utilizzata
- Con ```first-fit```, statisticamente si ha uno spreco del 30% della memoria
- Soluzione attraverso ==compattazione==
	- si possono spostare processi runtime per renderli contigui
	- funziona ma è inefficiente, soprattutto se ci sono tanti processi
![[Pasted image 20250404113313.png|600]]
### paging
- Memoria divisa in $N$ frame fissi della **stessa** dimensione 
- Processi divisi in **pagine** della stessa dimensione dei frame
![[Pasted image 20250404113526.png|400]]
- Quando un processo termina, i relativi frame vengono liberati
- Nuovo processo, suddiviso in pagine, può essere allocato sui frame liberi in modo **non contiguo**
	- permette di evitare **frammentazione esterna**
![[Pasted image 20250404113755.png|300]]
- Dimensione pagine multiplo di 2
	- tipicamente $4kB$
	- operazioni in binario molto veloci
#### schema di traduzione indirizzo logico a fisico
- Rappresentazione indirizzo logico in paging:
	- **page number**
		- indice pagina
	- **page offset**
		- indirizzi di offset all'interno della pagina
![[Pasted image 20250404114306.png|300]]
- Conversione attraverso **MMU** con **page table**
	- viene scambiato indice di pagina logica con indice di pagina fisica
![[Pasted image 20250404115148.png|300]]
#### frammentazione interna
- Pagine potrebbero non essere riempite completamente
- Caso **peggiore**
	- spreco di ```sizeof(frame) - 1 byte```
- Caso **medio**
	- spreco di ```sizeof(frame)/2```
- Si può ridurre frammentazione diminuendo la dimensione delle pagine, ma allo stesso tempo senza avere troppe pagine, altrimenti diventa difficile da gestire
#### allocazione frame liberi
- Viene mantenuta lista frame liberi
![[Pasted image 20250404115756.png|300]]
#### implementazione page table
- Viene tipicamente memorizzata in **memoria**
	- **Page-table base register** (PTBR)
	- **Page-table length register** (PTLR)
![[Pasted image 20250404115926.png|150]]
- Problema: ogni volta che devo accedere alla memoria devo fare **due** accessi in memoria
#### translation look-aside buffer
- Cache che tiene in memoria i valori di conversione della page table utilizzati più frequentemente
- La conversione prima cerca nel TLB e solo se non trova l'entry va in memoria
- Implementata attraverso **memoria associativa**
	- molto veloce
![[Pasted image 20250404120201.png|400]]
