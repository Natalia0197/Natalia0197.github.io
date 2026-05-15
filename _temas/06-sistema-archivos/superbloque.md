---
layout: post
title: "6.2.1 El Superbloque"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

El **superbloque** es una estructura fundamental del sistema de archivos que contiene su información de estado. Entre sus campos se encuentran:

- Tamaño del sistema de archivos
- Lista de bloques libres disponibles
- Índice del siguiente bloque libre
- Tamaño de la lista de inodos
- Total de inodos libres y lista de inodos libres
- Campos de bloqueo para acceso concurrente
- Banderas que indican si el superbloque ha sido modificado

Durante la operación del sistema, existe una **copia del superbloque en memoria** para acceso eficiente. El kernel mantiene hilos como `sync_supers` y `sync_filesystems` que periódicamente actualizan el disco con los cambios en memoria, garantizando la integridad de datos ante cierres inesperados.

En C, las funciones `sync()` y `syncfs()` permiten forzar la sincronización:

```c
#include <unistd.h>
void sync(void);      // sincroniza todo el sistema
int syncfs(int fd);   // sincroniza solo el sistema de archivos de fd
```

Los comandos `mount()` y `umount()` permiten añadir y remover sistemas de archivos:

```c
#include <sys/mount.h>
int mount(const char *source, const char *target,
          const char *filesystemtype, unsigned long mountflags,
          const void *data);
int umount(const char *target);
```

## ¿Cómo podría mejorar esta práctica?

Practicar con el comando `sync` en terminal y comparar el comportamiento antes y después. También explorar `/proc/filesystems` para ver todos los sistemas de archivos reconocidos por el kernel.

## Salida en pantalla

```bash
$ cat /proc/filesystems
nodev   sysfs
nodev   tmpfs
nodev   devtmpfs
        ext3
        ext4
        vfat
...
```