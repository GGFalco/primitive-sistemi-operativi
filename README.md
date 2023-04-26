# **Primitive in C**

## Creazione di un file
Operazione di rilascio (_epilogo_): restituisce la chiave (_indice nella **TFA**_)
```c
    int fd = creat(char* name, int mode)
```
`Return value` file descriptor (_chiave_) del file appena creato
`Errors`:
* Spazio insufficiente
* Non abbiamo diritti di scrittura
 
 

## Apertura di un file
Operazione di richiesta (_prologo_): consente di ottenere una chiave (_indice nella **TFA**_)
```c
    int fd = open(char* name, int flag)
```
