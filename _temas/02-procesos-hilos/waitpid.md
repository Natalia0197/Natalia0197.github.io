---
layout: post
title: "2.5.1 Uso de waitpid()"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Cuando se requiere esperar por un proceso hijo específico, o se necesita un control más fino del comportamiento de espera, se utiliza `waitpid()`. Esta llamada suspende la ejecución del proceso actual hasta que el proceso hijo especificado finaliza o hasta que ocurre un evento controlado por las opciones proporcionadas. Su prototipo es:

```c
#include <sys/types.h>
#include <sys/wait.h>
pid_t waitpid(pid_t pid, int *wstatus, int options);
```

## Valores del parámetro `pid`

- `-1`: espera por cualquier proceso hijo.
- `> 0`: espera por el hijo cuyo PID sea igual a `pid`.
- `0`: espera por cualquier hijo cuyo grupo de procesos sea igual al del proceso llamador.
- `< 0`: espera por cualquier hijo cuyo PGID sea igual al valor absoluto de `pid`.

## Opciones disponibles

El parámetro `options` permite modificar el comportamiento mediante las siguientes banderas:

- `WEXITED`: espera por hijos que hayan terminado.
- `WSTOPPED`: espera por hijos detenidos por una señal.
- `WNOHANG`: retorna inmediatamente si ningún hijo ha terminado.
- `WNOWAIT`: no elimina al hijo de la tabla de procesos.
- `WUNTRACED`: retorna si un hijo se ha detenido (aunque no esté siendo trazado).
- `WCONTINUED`: retorna si un hijo ha reanudado su ejecución tras recibir `SIGCONT`.

## Ejemplo

Programa que calcula el factorial de varios números usando procesos hijos y espera a cada uno con `waitpid()`:

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    pid_t hijo[5];
    int estado, i, j;
    long factorial = 1;

    for (j = 0; j < argc - 1; j++) {
        if ((hijo[j] = fork()) == -1) {
            perror("fallo el fork");
            exit(EXIT_FAILURE);
        } else if (hijo[j] == 0) {
            fprintf(stdout, "soy el hijo con pid = %ld\n", (long)getpid());
            for (i = atol(argv[j + 1]); i > 0; i--)
                factorial = factorial * i;
            fprintf(stdout, "El factorial es: %ld\n", factorial);
            sleep(2);
            exit(EXIT_SUCCESS);
        }
    }

    for (j = 0; j < argc - 1; j++) {
        if (waitpid(-1, &estado, 0) == -1)
            fprintf(stderr, "una señal debio interrumpir la espera\n");
        else
            fprintf(stdout, "el hijo:%d con pid %ld termino\n", j, (long)hijo[j]);
    }
    exit(EXIT_SUCCESS);
}
```

## ¿Qué aprendí?

Aprendí que `waitpid()` ofrece mayor flexibilidad que `wait()`, ya que permite esperar por un hijo específico usando su PID, o incluso retornar inmediatamente con `WNOHANG` si ningún hijo ha terminado. Esto es útil para implementar servidores concurrentes o gestores de tareas que no deben bloquearse indefinidamente.

## ¿Cómo podría mejorar esta práctica?

Podría usar la bandera `WNOHANG` para implementar un bucle de espera no bloqueante, lo que permitiría al padre continuar realizando otras tareas mientras verifica periódicamente si algún hijo ha terminado.

## Salida en pantalla

*Capturas pendientes de agregar.*