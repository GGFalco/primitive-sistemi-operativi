# **Primitive in C**
Qui di seguito le primitive studiate fino ad'ora in C per la programmazione di sistema.


## Creazione di un file
Operazione di rilascio (_epilogo_): restituisce la chiave (_indice nella **TFA**_).
```c
    #include <fcntl.h>
    
    int fd = creat(char* name, int mode)
```
`Return value` file descriptor (_chiave_) del file appena creato, altrimenti un valore < 0

`Errors`
* Spazio insufficiente
* Non abbiamo diritti di scrittura

 
 

## Apertura di un file
Operazione di richiesta (_prologo_): consente di ottenere una chiave (_indice nella **TFA**_).
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
Chide il file corrispondente al file descriptor `fd`. Alla terminazione del processo la avviene la chiusura automatica dei file.
```c
    #include <unistd.h>
    
    int ret = close(int fd)
```
`Return value` 0 in caso di successo, altrimenti < 0





## Lettura su un file
Leggiamo `n` byte dal file con file descriptor `fd`. I caratteri letti vengono inseriti nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    
    int nread = read(int fd, char* buffer, int n)
```
`ATTENZIONE` buffer **NON** è una stringa ma un _array di byte_

`Return value` restituisce il _numero di byte_ su cui ha lavorato, se ci sono problemi `nread` sarà diverso da `n`. Se il **file pointer** è sull'EOF ritorna 0




## Scrittura su un file
Scriviamo `n` byte sul file con file descriptor `fd`. I caratteri vengono presi nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    
    int nwrite = write(int fd, char* buffer, int n)
```
`ATTENZIONE` buffer **NON** è una stringa ma un _array di byte_
`Return value` restituisce il _numero di byte_ su cui ha lavorato, se ci sono problemi `nwrite` sarà diverso da `n`.
