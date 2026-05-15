---
layout: post
title: "2.5 Sistema de llamada wait()"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

La llamada al sistema `wait()` permite que un proceso padre se suspenda hasta que uno de sus procesos hijo termina su ejecución. Su prototipo es el siguiente:

```c
#include <sys/types.h>
#include <sys/wait.h>
pid_t wait(int *stat_loc);
```

Si `wait()` retorna debido a la terminación de un hijo:

- El valor de retorno es positivo y corresponde al PID del proceso hijo.
- En caso de error, retorna `-1` y se establece un valor apropiado en la variable global `errno`.

Algunos valores relevantes de `errno` son:

- `ECHILD`: el proceso no tiene hijos a los cuales esperar.
- `EINTR`: la llamada fue interrumpida por la recepción de una señal.

## Análisis del estado de terminación

El parámetro `stat_loc` es un apuntador a una variable entera donde el kernel almacena información sobre el estado de terminación del proceso hijo. Este valor debe analizarse utilizando los macros definidos en `<sys/wait.h>`:

- `WIFEXITED(*stat_loc)`: evalúa a verdadero si el proceso hijo terminó de forma normal.
- `WEXITSTATUS(*stat_loc)`: si el hijo terminó normalmente, obtiene los 8 bits menos significativos del valor pasado a `exit()`.
- `WIFSIGNALED(*stat_loc)`: evalúa a verdadero si el proceso hijo terminó debido a una señal no capturada.
- `WTERMSIG(*stat_loc)`: si el proceso hijo terminó por una señal, obtiene el número de dicha señal.

## Ejemplo

Programa que muestra el empleo de la llamada al sistema `wait()`:

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
    } else if (wait(&estado) != hijo) {
        fprintf(stderr, "una señal debio interrumpir la espera\n");
    } else {
        fprintf(stderr, "soy el padre con pid = %ld e hijo con pid = %ld\n",
                (long)getpid(), (long)hijo);
    }
    exit(EXIT_SUCCESS);
}
```

## ¿Qué aprendí?

Aprendí que `wait()` es fundamental para la sincronización entre procesos padre e hijo. Sin esta llamada, los procesos hijos que terminan antes que el padre quedan en estado zombi, ocupando una entrada en la tabla de procesos. El uso correcto de `wait()` permite recuperar el código de terminación del hijo y liberar completamente sus recursos del sistema.

## ¿Cómo podría mejorar esta práctica?

Podría combinar `wait()` con el análisis de los macros `WIFEXITED` y `WEXITSTATUS` para obtener e imprimir el código de salida del proceso hijo, lo que permitiría un control más detallado del resultado de la ejecución.

## Salida en pantalla

![wait](/assets/wait.png)
*Ejemplo 1*