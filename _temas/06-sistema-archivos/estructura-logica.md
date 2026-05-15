---
layout: post
title: "6.2 Estructura Logica del Sistema de Archivos"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

El sistema de archivos, visto de forma lógica, se divide en **4 secciones principales**:

1. **Boot**: Ubicado en el primer sector, contiene el código de arranque que busca el sistema operativo y lo carga en memoria para inicializarlo.

2. **Superbloque**: Describe el estado del sistema de archivos — tamaño total, cantidad de archivos, espacio libre, etc.

3. **Lista de nodos índice (inodos)**: Una entrada por cada archivo. Guarda su descripción, situación, propietario, permisos, etc.

4. **Bloque de datos**: Contiene el contenido real de los archivos referenciados por los inodos.

También aprendí a usar la función `statvfs()` en C para consultar estadísticas del sistema de archivos montado. La estructura `statvfs` expone campos como el tamaño de bloque (`f_bsize`), número de inodos disponibles (`f_favail`), y banderas de montaje (`f_flag`).

```c
#include <sys/statvfs.h>
struct statvfs vfs;
statvfs("/", &vfs);
printf("Tamaño de bloque: %ld\n", (long) vfs.f_bsize);
printf("Bloques libres: %lu\n", (unsigned long) vfs.f_bfree);
```

## ¿Cómo podría mejorar esta práctica?

Implementar el programa completo con `statvfs()` y mostrar todas las estadísticas del sistema de archivos en una salida formateada. También se podría explorar la diferencia entre `statvfs()` (estándar POSIX) y `statfs()` (solo Linux).

## Salida en pantalla

Ejemplo de salida del programa con `statvfs()`:

```
Archivo: /
Tamaño de bloques: 4096
Tamaño de fragmento: 4096
Bloques libres: 1204582
Bloques Disponibles: 1141073
Número de Inodos: 3932160
Número de Inodos Libres: 3721044
```