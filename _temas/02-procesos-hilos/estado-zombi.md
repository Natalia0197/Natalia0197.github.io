---
layout: post
title: "2.7 Estado Zombi"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Un proceso **zombi** es un proceso que ya terminó su ejecución, pero cuyo proceso padre no ha recogido su estado de salida mediante `wait()` o `waitpid()`. El proceso zombi no consume CPU ni memoria de usuario, pero sí ocupa una entrada en la tabla de procesos, conservando el PID, el código de salida e información estadística mínima.

## Caso 1: Proceso zombi sin `wait()`

Cuando el padre no llama a `wait()`, el hijo queda en estado zombi hasta que el padre lo recoja o termine.

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
    pid_t pid;
    pid = fork();

    if (pid == 0) {
        /* Proceso hijo */
        printf("Hijo terminado. PID=%ld\n", (long)getpid());
        exit(EXIT_SUCCESS);
    } else {
        /* Proceso padre */
        printf("Padre en ejecución. PID=%ld\n", (long)getpid());
        sleep(30); /* El padre NO llama a wait() */
    }
    return EXIT_SUCCESS;
}
```

**Qué ocurre paso a paso:**

1. El proceso padre crea un hijo con `fork()`.
2. El hijo finaliza inmediatamente con `exit(0)`.
3. El padre sigue vivo, pero no ejecuta `wait()`.
4. El kernel marca al hijo como `EXIT_ZOMBIE` y conserva su información para el padre.
5. El proceso hijo queda en estado zombi durante 30 segundos.

Para observar el zombi desde otra terminal mientras el padre duerme:

```bash
ps -el | grep Z
```

## Caso 2: Proceso no zombi usando `wait()`

Cuando el padre llama a `wait()`, recoge el estado del hijo y el kernel elimina completamente su entrada de la tabla de procesos.

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

int main(void) {
    pid_t pid;
    int status;
    pid = fork();

    if (pid == 0) {
        /* Proceso hijo */
        printf("Hijo terminado. PID=%ld\n", (long)getpid());
        exit(EXIT_SUCCESS);
    } else {
        /* Proceso padre */
        wait(&status); /* Recolecta al hijo */
        printf("Padre: hijo recolectado\n");
    }
    return EXIT_SUCCESS;
}
```

**Qué ocurre ahora:**

1. El hijo termina su ejecución.
2. El kernel marca al hijo como terminado.
3. El padre ejecuta `wait()`.
4. El kernel entrega el estado de salida al padre y elimina completamente al hijo de la tabla de procesos.
5. No existe estado zombi.

## Proceso huérfano

Si el proceso padre termina antes que el hijo, el hijo se vuelve **huérfano**. El kernel lo reasigna automáticamente al proceso con PID 1 (`init` o `systemd`), el cual ejecuta `wait()` automáticamente, evitando que quede zombi.

## ¿Qué aprendí?

Aprendí que el estado zombi es una consecuencia natural del ciclo de vida de los procesos en UNIX: el kernel conserva la información del hijo hasta que el padre la recoja. Aunque un zombi no consume recursos significativos, acumular muchos puede agotar la tabla de procesos. La solución es siempre llamar a `wait()` o `waitpid()` desde el proceso padre.

## ¿Cómo podría mejorar esta práctica?

Podría implementar un manejador de señal para `SIGCHLD` que llame automáticamente a `wait()` cada vez que un hijo termina, evitando así la acumulación de zombis en aplicaciones que crean muchos procesos hijos de forma dinámica.

## Salida en pantalla

![zombie1](/assets/zombie1.png)
*Ejemplo 1*

![zombie2](/assets/zombie2.png)
*Ejemplo 1*