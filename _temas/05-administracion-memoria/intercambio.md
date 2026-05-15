---
layout: post
title: "5.6 Intercambio"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

El **intercambio** (o *swapping*) es la técnica de mover procesos entre la memoria principal y el disco cuando la RAM no puede alojar a todos los procesos activos. En GNU/Linux, este espacio en disco se conoce como **swap**.

A diferencia de las particiones fijas, el intercambio utiliza **particiones variables**: cada proceso ocupa exactamente el espacio que necesita, lo cual reduce el desperdicio. Sin embargo, con el tiempo quedan huecos dispersos en la memoria que pueden ser demasiado pequeños para alojar nuevos procesos. Para resolver esto existe la **compactación de memoria**: mover todos los procesos hacia la parte baja de la RAM y unir todos los huecos en uno solo; aunque raramente se usa porque consume demasiado tiempo de CPU.

En C, las funciones `swapon()` y `swapoff()` permiten activar y desactivar el swap por programa:

```c
#include <unistd.h>
#include <sys/swap.h>
int swapon(const char *path, int swapflags);
int swapoff(const char *path);
```

Existen tres formas de llevar registro del uso de la memoria con particiones variables:
- **Mapas de bits** (sección 5.7)
- **Listas ligadas** (sección 5.8)
- Sistemas amigables (buddy system)

## ¿Cómo podría mejorar esta práctica?

Monitorear en tiempo real el uso del swap con `swapon -s` y `vmstat`, y observar cómo cambia bajo distintas cargas del sistema. También explorar `swappiness` para entender cuándo el kernel decide enviar páginas al swap.

## Salida en pantalla

```bash
$ swapon
NAME      TYPE      SIZE  USED  PRIO
/dev/dm-1 partition 976M  49.9M -2

$ cat /proc/sys/vm/swappiness
60
```

El valor 60 indica que el kernel empezará a usar el swap cuando la RAM llegue al 40% de uso.