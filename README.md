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

`Return value` 
- **file descriptor** (_chiave_) del file appena creato
- altrimenti un **valore < 0**

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

`Return value`
- **file descriptor** del file appena creato
- altrimenti un **valore < 0**

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
`Return value` 
- **0** in caso di successo
- altrimenti **< 0**





# Lettura su un file
Leggiamo `n` byte dal file con file descriptor `fd`. I caratteri letti vengono inseriti nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    
    int nread = read(int fd, void* buffer, int n)
```
`Return value` 
- **numero di byte** su cui ha lavorato
- altrimenti **< 0**
- Se il _file pointer_ è sull'EOF ritorna **0**
Se ci sono problemi `nread` è diverso da `n`. 

`ATTENZIONE` buffer **NON** è una stringa ma sarà un _array di byte_ nel caso di un _char*_




# Scrittura su un file
Scriviamo `n` byte sul file con file descriptor `fd`. I caratteri vengono presi nella memoria puntata da `buffer`.
```c
    #include <unistd.h>
    #include <stdio.h>
    
    int nwrite = write(int fd, void* buffer, int n)
```
`<stdio.h>` definisce la costante **BUFSIZ** per allocare un buffer statico

`Return value`
- **numero di byte** su cui ha lavorato
- altrimenti **< 0**
Se ci sono problemi `nwrite` è diverso da `n`.

`ATTENZIONE` buffer **NON** è una stringa ma sarà un _array di byte_ nel caso di un _char*_






# Muoversi su un file
Spostiamo il file pointer all'interno del file indicato dal `fd` di `offset` byte a partire dalla posizione `origin`. 
```c
    #define <unistd.h>
    
    long int newpos = lseek(long int fd, long int offset, int origin)
```
`origin` SEEK_SET(0), SEEK_CUR(1), SEEK_END(2)

`Return value` 
**numero di byte** a partire dall'_inizio_ del file fino al _file pointer_




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
**PID** del processo corrente

```c
    #include <unistd.h>
    
    int ppid = getppid()
```
`Return value`
**ParentPID**, ovvero PID del processo padre



# User Identifier
```c
    #include <unistd.h>
    
    int uid = getuid()
    int euid = geteuid()
```
`Return value`
**UID** del processo corrente
**Effective UID** del processo corrente


# Group Identifier
```c
    #include <unistd.h>
    
    int gid = getgid()
    int egid = getegid()
```
`Return value`
**GID** del processo corrente
**Effective GID** del processo corrente



# Sospensione di un processo
Sospendiamo un processo padre _in attesa_ della terminazione di uno dei processi figli.
```c
    #include <sys/wait.h>
    
    int pid = wait(int *status)
```
`status`
- <ins>Terminazione Normale</ins>
nel _byte alto_, valore di ritorno della **exit**
nel _byte basso_, **zero**

- <ins>Terminazione Anormale</ins>
nel _byte alto_, **zero**
nel _byte basso_, segnale che ha provocato la terminazione

`Return value`
- **PID** del figlio terminato
- **< 0** in caso non abbia figli da attendere




# Terminazione di un processo
Chiudiamo tutti i file aperti per il processo che termina.
```c
    #include <stdlib.h>
    
    void exit(int status)
```





# Esecuzione di un programma
In UNIX possiamo cambiare il programma che un processo sta eseguendo tramite una delle _primitive della famiglia **exec**_.

`Return value`
- **<0** in caso di errore
- **niente** altrimenti

`Osservazioni`
- Le primitive **non producono un nuovo processo**, ma lo _trasformano_ con il cambiamento dell'area utente del processo interessato, sia come _codice_ che come _dati_
- Si torna all'istruzione successiva **solo** in caso di <ins>errore</ins>

`Effetti`
- L'area _kernel_ viene copiata, i descrittori dei file aperti rimangono tali dopo la exec
- I _segnali_ vengono modificati (vedremo piu avanti)
- L'_effective UID_ potrebbe essere modificato se il **SUID** del programma da eseguire è uguale ad 1
- Si ereditano:
    - direttorio corrente
    - file aperti
    - maschera dei segnali
    - terminale di controllo e altre caratteristiche

## Versione V
Il nome del comando, le opzioni ed i parametri vengono passati tramite un array di stringhe.
```c
    #include <unistd.h>
    
    int execv(char* pathname, char** argv)
```

`pathname` cammino completo che individua un programma

`argv` vettore di stringhe che contiene per ogni posizione: {_comando_, [_opzioni_], [_parametro1_], ..., (_char* 0_)}


## Versione V + P
```c
    #include <unistd.h>
    
    int execvp(char* name, char** argv)
```

`name` nome relativo semplice che individua un programma

`argv` vettore di stringhe che contiene per ogni posizione: {_comando_, [_opzioni_], [_parametro1_], ..., (_char* 0_)}


## Versione L
Il nome del comando, le opzioni ed i parametri vengono passati direttamente come parametri alla primitiva.
```c
    #include <unistd.h>
    
    int execl(char* pathname, char* argv0, ..., char* argvn-1, (char*) 0)
```

`pathname` cammino completo che individua un programma

`argv0` nome del programma da eseguire (_per convenzione_), gli altri sono le opzioni ed i parametri


## Versione L + P
```c
    #include <unistd.h>
    
    int execlp(char* name, char* argv0, ..., char* argvn-1, (char*) 0)
```

`name` nome relativo semplice che individua un programma

`argv0` nome del programma da eseguire (_per convenzione_), gli altri sono le opzioni ed i parametri



# Gestione degli errori
In caso di fallimento le System Call ritornano un valore negativo.
```c
   void perror(const char* str)
```
Esempio:
```c
    int main() {
        
        int fd;
        if((fd = open("file.txt", O_RDONLY)) < 0) {
            perror("Errore: ");
        }
        
        exit(0);
    }
```
Output: su **standard ERROR**
```
Errore: No such file or directory
```

In UNIX viene modificata anche la variabile globale **errno** con il codice di errore occorso alla system call
```c
    #include <errno.h>
    
    extern int errno;
```


# Creazione di una Pipe
Creiamo due nuovi elementi nella tabella dei file aperti, che ci servono per il piping.
```c
    #include <unistd.h>
    
    int ret = pipe(int piped[2])
```

`piped` 

array in cui vengono memorizzati i rispettivi file descriptor:
- piped[0] : file descriptor per la _lettura_
- piped[1] : file descriptor per la _scrittura_

`Return value`

- **0** in caso di successo
- **< 0** in caso di insuccesso

`Verso della pipe`

Il verso è **unidirezionale**. Supponiamo l'esempio in cui il padre deve leggere ed il figlio deve scrivere:
- il padre <ins>chiude</ins> la scrittura
- il figlio <ins>chiude</ins> la lettura



# Duplicazione di un file descriptor
Copiamo l'elemento di indice `fd` della _Tabella dei File Aperti_ nella prima posizione libera
```c
    #include <unistd.h>
    
    int ret = dup(int fd)
```

`fd` file descriptor da duplicare

`Return value`
- **il nuovo file descriptor**
- **< 0** in caso di insuccesso



# Gestione di un segnale
Definiamo un **handler** di un segnale, può trattarsi di:
- `Funzione Handler` una funzione che riceve come parametro un **int** e ritorna **void**
- `Azione di Default` funzione MACRO **SIG_DFL**
- `Ignorare` funzione MACRO **SIG_IGN**

All'occorrenza del segnale `signal` viene invocata la funzione `func`
```c
    #include <signal.h>
    
    void (* signal( int signal, void (*func)(int) ) ) (int)
```

oppure
```c
    #include <signal.h>
    
    typedef void (*sighandler_t)(int);
    
    sighandler_t signal(int signal, sighandler_t func)
```

`Osservazioni`
- Dopo una fork() i processi figli ereditano le funzioni handler del padre
- Dopo una exec() il processo che viene trasformato, aggancia a funzioni di default quelle che prima erano state assegnate a funzioni handler


# Invio di un segnale ad un altro processo
I processi possono inviare segnali ad altri processi. Il processo invia il segnare `sig` al processo con `pid` specificato
```c
    #include <signal.h>
    #include <sys/types.h>
    
    int ret = kill(int pid, int sig)
```

`Return value`
- **0** in caso di successo
- **< 0** in caso di insuccesso


# Sospensione di un processo
Sospendiamo un processo fino alla ricezione di un qualunque segnale
```c
    #include <unistd.h>
    
    int ret = pause()
```


# Sospesione temporizzata di un processo
Sospendiamo un processo per `ns` secondi
```c
    #include <unistd.h>
    
    unsigned int sleep(unsigned int ns)
```

`Return value`
- **0** se la sospensione non è stata interrotta da segnali
- **ns - n** se il risveglio è stato causato da un segnale al tempo `n`


# Installazione di un allarme
Impostiamo un timer che dopo `ns` secondi invierà al processo il segnale **SIGALRM**
```c
    #include <unistd.h>
    
    unsigned int alarm(unsigned int ns)
```
