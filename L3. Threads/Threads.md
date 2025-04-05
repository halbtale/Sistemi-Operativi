## lab: threads
### scheduler activation
- Schema di comunicazione tra libreria **user-thread** e **kernel-thread**
	- utilizzato in many-to-many e modelli a due livelli
- Utilizzo **Lightweight process** (LWP)
	- **struttura dati** intermedia che mappa user thread a kernel thread
	- **CPU virtuale** su cui la libreria di thread può fare lo scheduling di un user thread
	- **mappatura** a **thread** **fisico** avviene da parte del **sistema operativo**
![[Pasted image 20250403121614.png|200]]

### pthread - gestione thread
- Permette gestione di **user threads**
#### inzializzazione attributi di default
```c
pthread_attr_t attr;
pthread_attr_init(&attr);
```
#### creazione threads
```c
void *runner1(void *param);

pthread_t tid1; 
pthread_create(&tid1, &attr, runner1, param);
```
#### attesa terminazione thread (nel parent)
```c
pthread_join(tid1, NULL);
```
#### terminazione thread (all'interno del runner)
```c
pthread_exit(0);
```

>[!info] Compilazione
>- Utilizzo flag ```-lpthread```

### pthread - therad scheduling policy
- ```policy```: 
	- ```SCHED_FIFO```
		- fcfs con priorità
	- ```SCHED_RR```
		- round robin con priorità
	- ```SCHED_OTHER```
		- time sharing
#### ottenere policy corrente
```c
int policy;

pthread_attr_getschedpolicy(&attr, &policy);
```
#### impostare policy
```c
pthread_attr_setinheritsched(&attr, PTHREAD_EXPLICIT_SCHED); 
pthread_attr_setschedpolicy(&attr, policy);
```
### pthread - thread contention scope
- Tipologie:
	- **process-contention scope** (**PCS**)
		- thread compete contro altri thread del processo
		- ```PTHREAD_SCOPE_PROCESS```
	- **system-contention scope** (**SCS**)
		- thread compete contro tutte le task nel sistema
		- unico disponibile in linux/mac
		- ```PTHREAD_SCOPE_SYSTEM```
#### ottenere scope corrente
```c
int scope;

pthread_attr_getscope(&attr,&scope);
```
#### impostare scope
```c
pthread_attr_setscope(&attr, scope);
```