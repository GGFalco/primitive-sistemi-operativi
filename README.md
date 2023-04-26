# **Primitive in C**

## Creazione di un file
Operazione di rilascio (_epilogo_): restituisce la chiave (_indice nella **TFA**_)
```c
    int fd = creat(char* name, int mode)
```
**Return value:** file descriptor (_chiave_) del file appena creato

## Apertura di un file
```c
    int fd = open(char* name, int flag)
```
