---
layout: post
title: "2.6 Sistema de llamada _exit() y exit()"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Todo proceso debe terminar su ejecución en algún momento, ya sea de forma normal o anormal. Para la terminación normal existen dos llamadas principales: `_exit()` y `exit()`.

## `_exit()`

La llamada al sistema `_exit()` termina el proceso de forma inmediata, devolviendo el estado de terminación al proceso padre. Su prototipo es:

```c
#include <unistd.h>
void _exit(int status);
```

El argumento `status` define el estado de terminación del proceso, disponible para el padre cuando invoca `wait()`. Solo los 8 bits menos significativos del estado están disponibles para el padre. Por convención:

- Un estado de `0` indica que el proceso se completó correctamente.
- Un valor distinto de cero indica que el proceso terminó con error.

## `exit()`

La función `exit()` realiza varias acciones antes de invocar internamente a `_exit()`. Se encarga de retirar los recursos que está utilizando el proceso, dejarlo preparado para su eliminación, quitarlo del planificador e indicar su terminación al padre mediante la señal `SIGCHLD`. Su prototipo es:

```c
#include <stdlib.h>
void exit(int status);
```

En GNU/Linux, si el proceso que termina no tuviera padre (porque este acabó antes), sería adoptado por el proceso 1 (`init` o `systemd`) y eliminado directamente del planificador. Al pasar de estado activo a terminado, el proceso atraviesa un estado transitorio conocido como **zombi**.

## Diferencia entre `exit()` y `_exit()`

| Característica | `exit()` | `_exit()` |
|---|---|---|
| Ejecuta funciones registradas con `atexit()` | Sí | No |
| Vacía los buffers de E/S | Sí | No |
| Cierra descriptores de archivo | Sí | No |
| Termina inmediatamente | No | Sí |

## Ejemplo

Programa que ilustra el uso de `exit()` con `wait()`:

```c
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

int main() {
    pid_t hijo;
    int estado;

    if ((hijo = fork()) == -1) {
        perror("fallo el fork");
        exit(EXIT_FAILURE);
    } else if (hijo == 0) {
        fprintf(stderr, "soy el hijo con pid = %ld\n", (long)getpid());
        exit(EXIT_SUCCESS);
    } else {
        wait(&estado);
        if (WIFEXITED(estado))
            fprintf(stderr, "hijo termino con estado: %d\n", WEXITSTATUS(estado));
    }
    exit(EXIT_SUCCESS);
}
```

## ¿Qué aprendí?

Aprendí que `exit()` es la forma preferida de terminar un proceso en C porque garantiza que los buffers de salida sean vaciados y los recursos liberados correctamente. La diferencia con `_exit()` es importante en procesos hijo creados con `fork()`, donde se recomienda usar `_exit()` para evitar que el hijo vacíe buffers que también pertenecen al padre.

## ¿Cómo podría mejorar esta práctica?

Podría registrar funciones con `atexit()` para demostrar que `exit()` las ejecuta al terminar, mientras que `_exit()` no lo hace, evidenciando así la diferencia entre ambas llamadas de forma práctica.

## Salida en pantalla

![exit1](/assets/exit.png)
*Ejemplo1.*