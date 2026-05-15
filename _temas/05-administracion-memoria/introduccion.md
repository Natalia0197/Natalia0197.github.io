---
layout: post
title: "5.1 Introducción"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

Una de las tareas más importantes y complejas de un sistema operativo es la **administración de memoria**. Su gestión implica tratar la memoria principal como un recurso para asignarlo y compartirlo entre varios procesos activos, manteniendo en ella la mayor cantidad posible. Para ello el sistema operativo lleva un registro de las partes de memoria que se están utilizando y aquellas que no, con la finalidad de asignar y liberar espacio a los procesos, así como administrar el intercambio entre la memoria principal y el disco cuando esta no pueda albergar a todos los procesos.

Las herramientas básicas de la administración de memoria son la **paginación** y la **segmentación**:

- En la **paginación**, cada proceso se divide en páginas de tamaño constante y relativamente pequeño.
- La **segmentación** permite el uso de partes de tamaño variable.
- También es posible combinar ambas técnicas en un único esquema.

## Tamaño de página

El tamaño de página estándar en arquitecturas x86 y x86-64 es de **4 KB (4096 bytes)**. Esta unidad mínima de memoria (Entrada de la Tabla de Páginas, PTE) gestiona el mapeo entre la memoria física y virtual. Linux también admite HugePages de 2 MB a 1 GB para optimizar el rendimiento y reducir la carga del buffer de traducción anticipada (TLB).

Para obtener el tamaño de página en C se pueden usar `sysconf()` o `getpagesize()`:

```c
#include <unistd.h>
long sysconf(int name);
int  getpagesize(void);
```

## Ejemplo: obtener el tamaño de página

```c
#include <stdlib.h>
#include <stdio.h>
#include <unistd.h>

int main() {
    printf("Tamaño de página (sysconf):    %d bytes\n", (int)sysconf(_SC_PAGESIZE));
    printf("Tamaño de página (getpagesize): %d bytes\n", (int)getpagesize());
    return EXIT_SUCCESS;
}
```

Desde la terminal también se puede consultar con:

```bash
getconf PAGESIZE
```

## ¿Qué aprendí?

Aprendí que la administración de memoria es fundamental para la multiprogramación: sin ella, no sería posible que varios procesos convivieran en memoria de forma segura y eficiente. El concepto de página es la unidad básica de todo el sistema de memoria virtual, y conocer su tamaño es el punto de partida para entender cómo el kernel asigna y protege la memoria entre procesos.

## ¿Cómo podría mejorar esta práctica?

Podría explorar el archivo `/proc/meminfo` para visualizar en tiempo real cómo el sistema distribuye la memoria entre RAM libre, buffers, caché y swap, correlacionando esos valores con la salida del programa anterior.

## Salida en pantalla

![intro](/assets/admiintro.png)
*Ejemplo1.*