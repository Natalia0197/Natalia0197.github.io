---
layout: post
title: "6.4 Dispositivos de Entrada y Salida"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

El sistema operativo administra los accesos a dispositivos controlando tiempos de búsqueda, acceso y transferencia. En UNIX/Linux, todos los dispositivos se gestionan como archivos ubicados en `/dev`.

Los **módulos del kernel** que gestionan la comunicación con dispositivos se conocen como **controladores o drivers**. Para listarlos se usa `lsmod` o `lspci -k`.

**Dispositivos de bloque**: trabajan con bloques de al menos 512 bytes. Incluyen discos duros y memorias USB. El kernel usa un buffer caché para acelerar las transferencias. Ejemplo con `hdparm`:
```bash
$ sudo hdparm -t /dev/sda
Timing buffered disk reads: 186 MB in 3.01 seconds = 61.83 MB/sec
```

**Dispositivos de carácter**: trabajan con flujos de bytes individuales sin buffer caché, a baja velocidad. Incluyen terminales, teclados e impresoras. Cada usuario conectado tiene una terminal `/dev/tty##`.

**Dispositivos virtuales (pseudo-dispositivos)**: gestionados por el kernel sin hardware físico, como `/dev/mem`, `/dev/null`, `/dev/zero`.

Los archivos de dispositivos tienen asociados en su inodo el **número mayor** (tipo de dispositivo) y el **número menor** (unidad específica), en lugar de bloques de datos. El kernel los usa para localizar el driver correspondiente.

```c
struct stat sb;
stat("/dev/sda", &sb);
printf("Mayor: %u\n", major(sb.st_rdev));
printf("Menor: %u\n", minor(sb.st_rdev));
```

Para conocer la terminal donde está conectado un usuario se usa `/etc/utmp` con la función `getutent()`:
```c
#include <sys/utmp.h>
struct utmp *getutent(void);
```

## ¿Cómo podría mejorar esta práctica?

Desarrollar un programa que liste todos los dispositivos de bloque y carácter en `/dev` mostrando su número mayor y menor, usando `stat()` y las funciones `major()` y `minor()`.

## Salida en pantalla

```bash
$ lsblk -d -o NAME,MAJ:MIN,SIZE,TYPE
NAME  MAJ:MIN  SIZE TYPE
sda     8:0   500G disk
sdb     8:16   32G disk

$ ls -l /dev/tty*
crw-rw-rw- 1 root dialout 4, 0 May 15 tty0
crw--w---- 1 root tty     4, 1 May 15 tty1
```