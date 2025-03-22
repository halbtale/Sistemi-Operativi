## lab: processi
### creazione di un processo
```c
pid_t pid = fork()
```
- Crea un **processo child** come **copia** esatta del processo parent
- Output
	- ```pid > 0```
		- processo parent
		- contiene _pid_ del child creato
	- ```pid == 0```
		- processo child
	- ```pid < 0```
		- fork fallita
### ottenere pid processo attuale
```c
pid_t current_process_pid = getpid()
```
### wait system call
```c
wait(NULL)
```
- Permette al parent di **attendere** che il processo child termini
	- se ci sono più children, attende che uno di quelli termini
- **Processo orfano**
	- se il parent non chiama wait e termina prima del child, quest'ultimo diventa orfano
	- assegnato a systemd
		- si può vedere con comando ```pstree```
### caricare un eseguibile nel child
```c
execlp("/command/path","command","args")
```
#### esempio
- Per eseguire comando ```ls```:
```c
execlp("/bin/ls","ls",NULL);
```
### comando usleep
```c
usleep(time);
```
- Permette di **bloccare** l'esecuzione del codice per un dato tempo
- Argomenti
	- ```time```in millisecondi
