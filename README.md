# **Primitive in C**
Qui di seguito le primitive studiate fino ad'ora in C per la programmazione di sistema.


# Creazione di un file
Operazione di rilascio (_epilogo_): crea il file di nome `name` con i diritti `mode` (_in ottale_)
```c
    #include <fcntl.h>
    #define PERM 0644
    
    int fd = creat(char* name, int mode)
```
`PERM` diritti in ottale

`mode` = mode & ~umask (=0022). Significa che _mode_ sarà 0644

`Return value` file descriptor (_chiave_) del file appena creato, altrimenti un valore < 0

`Errors`
- Spazio insufficiente
- Non abbiamo diritti di scrittura



 
 

# Apertura di un file
Operazione di richiesta (_prologo_): apre il file di nome `name` in modalità `flag`
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



# Chiusura di un file
Chide il file corrispondente al file descriptor `fd`. Alla terminazione del processo avviene la chiusura automatica dei file.
```c
    #include <unistd.h>
    
    int ret = close(int fd)
```
`Return value` 0 in caso di successo, altrimenti < 0





# Lettura su un file
Leggiamo `n` byte dal file con file descriptor `fd`. I caratteri letti vengono inseriti nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    
    int nread = read(int fd, void* buffer, int n)
```
`ATTENZIONE` buffer **NON** è una stringa ma un _array di byte_ (char*)

`Return value` restituisce il _numero di byte_ su cui ha lavorato, altrimenti < 0. Se ci sono problemi `nread` è diverso da `n`. Se il **file pointer** è sull'EOF ritorna 0




# Scrittura su un file
Scriviamo `n` byte sul file con file descriptor `fd`. I caratteri vengono presi nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    #include <stdio.h>
    
    int nwrite = write(int fd, void* buffer, int n)
```
`ATTENZIONE` buffer **NON** è una stringa ma un _array di byte_ (char*)

`Return value` restituisce il _numero di byte_ su cui ha lavorato, altrimenti < 0. Se ci sono problemi `nwrite` è diverso da `n`.

`<stdio.h>` definisce la costante **BUFSIZ** per allocare un buffer statico






# Muoversi su un file
Spostiamo il file pointer all'interno del file indicato dal `fd` di `offset` byte a partire dalla posizione `origin`. 
```c
    #define <unistd.h>
    
    long int newpos = lseek(long int fd, long int offset, int origin)
```
`origin` SEEK_SET(0), SEEK_CUR(1), SEEK_END(2)

`Return value` numero di byte a partire dall'inizio del file fino al file pointer
