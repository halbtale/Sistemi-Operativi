## lab: moduli kernel
### creazione di un modulo kernel
#### entry point
- ```module_init(simple_init);```
	- macro per registrare entry point
- ```int simple_init()```
	- funzione che viene chiamata quando il modulo viene caricato nel kernel
	- deve restituire lo stato (0 se ha successo)
#### exit point
- ```module_exit(simple_exit);```
	- macro per registrare exit point
- ```void simple_exit()```
	- funzione che viene chiamata quando il modulo viene rimosso dal kernel
#### stampare messaggi sul log
- ```printk(KERN_INFO "Hello world\n")```
	- si usa in kernel mode
	- accetta una flag di priorità
		- ```KERN_INFO``` = information message
#### metadati modulo
```
MODULE_LICENSE("GPL"); 
MODULE_DESCRIPTION("Simple Module"); 
MODULE_AUTHOR("Alberto");
```

### comandi gestione moduli kernel
- ```lsmod```
	- **elenco** moduli kernel attivi
- ```sudo insmod [modulename.ko]```
	- **aggiungi** modulo nel kernel
- ```sudo rmmod [modulename]```
	- **rimuovi** modulo dal kernel
- ```sudo dmesg```
	- mostra il **log** del kernel
	- ```-c```
		- pulisci log