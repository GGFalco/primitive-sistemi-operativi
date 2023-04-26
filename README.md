# **Primitive in C**

## Creazione di un file
Operazione di rilascio (_epilogo_): restituisce la chiave (_indice nella **TFA**_)
```c
    int fd = creat(char* name, int mode)
```

## Apertuna di un file
```c
    int fd = open(char* name, int flag)
```
