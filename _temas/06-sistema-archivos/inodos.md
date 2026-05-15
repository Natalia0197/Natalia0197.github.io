---
layout: post
title: "6.2.2 Nodos Indices - Inodos"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

Cada archivo en un sistema UNIX/Linux tiene asociado un **inodo** que contiene toda la información necesaria para que un proceso acceda al archivo. Esta información incluye:

- Identificador del propietario (usuario y grupo)
- Derechos de acceso (permisos)
- Tipo de archivo
- Tamaño del archivo
- Fechas de creación, modificación y acceso
- Número de enlaces (links) del archivo
- Entradas para los bloques de dirección de datos
- Estado del inodo en memoria (bloqueado, modificado, etc.)

Una observación clave: **el nombre del archivo NO está en el inodo**. El nombre es solo una etiqueta en el directorio que mapea hacia el número de inodo.

Durante el arranque, el kernel carga la lista de inodos del disco en memoria como la **tabla de inodos**. Las operaciones del sistema de archivos trabajan con esta tabla en memoria; el kernel la sincroniza periódicamente al disco mediante hilos de `writeback`.

En C, las funciones `stat()`, `fstat()` y `lstat()` permiten obtener la información del inodo de un archivo:

```c
#include <sys/stat.h>
int stat(const char *pathname, struct stat *statbuf);
int fstat(int fd, struct stat *statbuf);
int lstat(const char *pathname, struct stat *statbuf);
```

La estructura `stat` contiene campos como `st_ino` (número de inodo), `st_mode` (tipo y permisos), `st_size` (tamaño), `st_uid` (propietario), entre otros.

## ¿Cómo podría mejorar esta práctica?

Crear un programa que liste todos los archivos de un directorio mostrando su número de inodo, tipo, permisos y tamaño. Comparar el inodo de un archivo original y un hard link para verificar que comparten el mismo número.

## Salida en pantalla

Programa que muestra características de archivos en el directorio actual:

```
Ruta actual: /home/usuario/prueba
Mostrar contenido
archivo.c   ID del dispositivo:[8,1]
Tipo de archivo: Regular
I-nodo: 148392
Modo: 100644 (octal)
No. Link: 1
Propietario: UID=1000  GID=1000
Tamaño: 512 bytes
Ultima fecha de modificación: Fri May 15 10:22:01 2026
```