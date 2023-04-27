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
`Return value` restituisce il _numero di byte_ su cui ha lavorato, altrimenti < 0. Se ci sono problemi `nread` è diverso da `n`. Se il **file pointer** è sull'EOF ritorna 0

`ATTENZIONE` buffer **NON** è una stringa ma sarà un _array di byte_ nel caso di un _char*_




# Scrittura su un file
Scriviamo `n` byte sul file con file descriptor `fd`. I caratteri vengono presi nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    #include <stdio.h>
    
    int nwrite = write(int fd, void* buffer, int n)
```
`<stdio.h>` definisce la costante **BUFSIZ** per allocare un buffer statico

`Return value` restituisce il _numero di byte_ su cui ha lavorato, altrimenti < 0. Se ci sono problemi `nwrite` è diverso da `n`.

`ATTENZIONE` buffer **NON** è una stringa ma sarà un _array di byte_ nel caso di un _char*_






# Muoversi su un file
Spostiamo il file pointer all'interno del file indicato dal `fd` di `offset` byte a partire dalla posizione `origin`. 
```c
    #define <unistd.h>
    
    long int newpos = lseek(long int fd, long int offset, int origin)
```
`origin` SEEK_SET(0), SEEK_CUR(1), SEEK_END(2)

`Return value` numero di byte a partire dall'inizio del file fino al file pointer




# Creazione di un processo
Il processo corrente genera un sotto processo figlio. Dopo la generazione si hanno 2 <ins>processi concorrenti</ins>. 
```c
    #include <unistd.h>
    
    int pid = fork()
```
`Return value` 
- **0** al processo figlio
- **PID** del processo figlio al padre 
- Un valore **< 0** in caso di errore


`Effetti`
1. Nuova entry nella _tabella dei processi_. Stesso `UID`, `GID` e stesso `PC`
2. Stesso codice del padre. _Incremento_ il contatore nella **text table**
3. _Area dati utente_ come <ins>copia</ins> dell'area dati del padre
4. _Area kernel_ come <ins>copia</ins> dell'area kernel del padre
5. _Descrittore del processo figlio_ nella coda dei processi pronti

`Osservazioni`
- Le variabili e puntatori sono <ins>copiati</ins> e quindi **NON** vengono condivisi, ma duplicati
- I file aperti risultato <ins>condivisi</ins> e quindi il _file pointer_ si sposta per tutta la famiglia di processi


# Process Identifier
```c
    #include <unistd.h>
    
    int pid = getpid()
```
`Return value`
PID del processo corrente

```c
    #include <unistd.h>
    
    int ppid = getppid()
```
`Return value`
ParentPID, ovvero PID del processo padre



# User Identifier
```c
    #include <unistd.h>
    
    int uid = getuid()
    int euid = geteuid()
```
`Return value`
UID del processo corrente
Effective UID del processo corrente


# Group Identifier
```c
    #include <unistd.h>
    
    int gid = getgid()
    int egid = getegid()
```
`Return value`
GID del processo corrente
Effective GID del processo corrente



# Sospensione di un processo
Sospendiamo un processo padre _in attesa_ della terminazione di uno dei processi figli
```c
    #include <sys/wait.h>
    
    int pid = wait(int *status)
```
`status`
- Terminazione Normale
nel _byte alto_, valore di ritorno della **exit**
nel _byte basso_, **zero**

- Terminazione Anormale
nel _byte alto_, **zero**
nel _byte basso_, segnale che ha provocato la terminazione

`Return value`
**PID** del figlio terminato
**< 0** in caso non abbia figli da attendere




# Terminazione di un processo
Chiudiamo tutti i file aperti per il processo che termina
```c
    #include <stdlib.h>
    
    void exit(int status
```
