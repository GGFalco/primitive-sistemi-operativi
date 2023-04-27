# **Primitive in C**
Qui di seguito le primitive studiate fino ad'ora in C per la programmazione di sistema.


## Creazione di un file
Operazione di rilascio (_epilogo_): restituisce la chiave (_indice nella **TFA**_)
```c
    #include <fcntl.h>
    
    int fd = creat(char* name, int mode)
```
`Return value` file descriptor (_chiave_) del file appena creato, altrimenti un valore < 0

`Errors`
* Spazio insufficiente
* Non abbiamo diritti di scrittura

 
 

## Apertura di un file
Operazione di richiesta (_prologo_): consente di ottenere una chiave (_indice nella **TFA**_)
```c
    #include <fcntl.h> 
    
    int fd = open(char* name, int flag)
```
`flag` **O_RDONLY, O_WRONLY, O_RDWR**

`Return value` file descriptor del file appena creato, altrimenti un valore < 0

`Errors`
- File non esistente
- Mancanza del diritto di lettura
- Esaurito lo spazio nella TFA (_Tabella File Aperti_)



## Chiusura di un file
Chide il file corrispondente al file descriptor `fd`
```c
    #include <unistd.h>
    
    int ret = close(int fd);
```
`Return value` 0 in caso di successo, altrimenti < 0
