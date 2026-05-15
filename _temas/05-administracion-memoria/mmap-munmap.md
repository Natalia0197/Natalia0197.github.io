---
layout: post
title: "5.10.2 Funciones mmap y munmap"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

Las funciones `mmap()` y `munmap()` permiten **mapear archivos o dispositivos directamente en el espacio de memoria virtual** de un proceso, eliminando la necesidad de copiar datos entre espacio de usuario y kernel:

```c
#include <sys/mman.h>

void *mmap(void *addr, size_t length, int prot, int flags,
           int fd, off_t offset);

int munmap(void *addr, size_t length);
```

**Parámetros de `mmap()`:**
- `addr`: dirección sugerida de inicio (normalmente `NULL`, el kernel elige)
- `length`: tamaño del mapeo en bytes (mayor que 0)
- `prot`: protecciones — `PROT_READ`, `PROT_WRITE`, `PROT_EXEC`
- `flags`: comportamiento — `MAP_SHARED` (cambios visibles a otros), `MAP_PRIVATE` (copia privada), `MAP_ANONYMOUS` (sin archivo)
- `fd`: descriptor de archivo a mapear (-1 para `MAP_ANONYMOUS`)
- `offset`: desplazamiento dentro del archivo

`munmap()` elimina el mapeo; acceder a esa región después provoca `SIGSEGV`.

**Usos comunes:**
- Mapear archivos grandes para acceso aleatorio eficiente
- Comunicación entre procesos (memoria compartida con `MAP_SHARED`)
- Asignar bloques grandes de memoria (`MAP_ANONYMOUS | MAP_PRIVATE`)

Para inspeccionar los mapeos de un proceso:
```bash
cat /proc/[pid]/maps
cat /proc/[pid]/smaps   # con estadísticas detalladas
```

También se puede ver la distribución física de la RAM del sistema:
```bash
sudo cat /proc/iomem
```

## ¿Cómo podría mejorar esta práctica?

Implementar un programa que use `mmap()` con `MAP_ANONYMOUS | MAP_SHARED` para compartir un bloque de memoria entre procesos padre e hijo creados con `fork()`, y observar cómo las escrituras de uno son visibles en el otro.

## Salida en pantalla

```bash
$ cat /proc/$$/maps | head -8
55a1c3400000-55a1c3401000 r--p 00000000 08:01 /usr/bin/bash
55a1c3401000-55a1c34cb000 r-xp 00001000 08:01 /usr/bin/bash
55a1c34cb000-55a1c34fc000 r--p 000cb000 08:01 /usr/bin/bash
7f3a12000000-7f3a12200000 rw-p 00000000 00:00 [heap]
7ffcb8800000-7ffcb8821000 rw-p 00000000 00:00 [stack]
```

Columnas: rango de direcciones virtuales | permisos | offset | dispositivo | inodo | nombre