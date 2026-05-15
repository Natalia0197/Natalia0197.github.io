---
layout: post
title: "6.3 Tipos de Archivos en Linux"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

En Linux existen **cuatro tipos principales de archivos**:

**1. Archivos ordinarios (regulares)**
Contienen bytes organizados como un arreglo lineal. Se pueden leer, escribir, añadir bytes al final o truncar. No se permite insertar bytes en medio ni borrar bytes individuales.

**2. Directorios**
Permiten estructurar jerárquicamente el sistema de archivos. Su función es relacionar nombres de archivos con su número de inodo correspondiente. El par inodo-nombre es conocido como *enlace (link)*. Solo el kernel puede modificar directorios directamente.

**3. Archivos especiales (de dispositivos)**
Permiten a los procesos comunicarse con periféricos. Hay dos subtipos:
- **Modo bloque**: trabajan con conjuntos de 512 bytes (ej. discos duros, memorias USB).
- **Modo carácter**: información como secuencia de bytes sin buffer caché (ej. terminales, teclados, impresoras).

**4. Archivos de comunicación (tuberías/pipes)**
Similares a archivos ordinarios, pero sus datos son **transitorios**. Se usan para comunicar procesos en modo FIFO (first in, first out). La sincronización del acceso es responsabilidad del kernel.

Para identificar el tipo de archivo en C, se usa el campo `st_mode` de la estructura `stat` con las máscaras definidas:

```c
switch (sb.st_mode & S_IFMT) {
    case S_IFREG:  printf("Regular\n");                break;
    case S_IFDIR:  printf("Directorio\n");             break;
    case S_IFCHR:  printf("Dispositivo de Caracter\n");break;
    case S_IFBLK:  printf("Dispositivo de Bloque\n");  break;
    case S_IFIFO:  printf("FIFO/pipe\n");              break;
    case S_IFLNK:  printf("Enlace simbólico\n");       break;
    case S_IFSOCK: printf("Socket\n");                 break;
}
```

Para listar el contenido de un directorio en C se usan `opendir()` y `readdir()`:

```c
DIR *dir = opendir("/home/usuario");
struct dirent *entrada;
while ((entrada = readdir(dir)) != NULL)
    printf("%s\n", entrada->d_name);
closedir(dir);
```

## ¿Cómo podría mejorar esta práctica?

Extender el programa para mostrar el tipo de cada archivo junto a su nombre, usando tanto las macros `S_ISREG`, `S_ISDIR`, etc., como el campo `d_type` de la estructura `dirent`.

## Salida en pantalla

```bash
$ ls -l /dev | head -10
crw-rw-rw-  1 root  tty  5, 0 May 15 10:00 tty
brw-rw----  1 root disk  8, 0 May 15 10:00 sda
brw-rw----  1 root disk  8, 1 May 15 10:00 sda1
prw-r--r--  1 usuario  0 May 15 10:00 mi_pipe
```
La primera letra indica: `c`=carácter, `b`=bloque, `p`=pipe, `-`=regular, `d`=directorio.