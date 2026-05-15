---
layout: post
title: "2.4 Sistema de llamado para identificar procesos"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Todo proceso en un sistema operativo tipo UNIX tiene asociado un identificador único denominado **PID** (Process Identifier), el cual es un número entero positivo asignado por el kernel. Asimismo, cada proceso mantiene una referencia al proceso que lo creó, conocido como proceso padre, cuyo identificador se denomina **PPID** (Parent Process Identifier).

Para obtener estos valores, el sistema proporciona las siguientes llamadas:

```c
#include <sys/types.h>
#include <unistd.h>

pid_t getpid(void);   /* Retorna el PID del proceso actual */
pid_t getppid(void);  /* Retorna el PID del proceso padre */
```

El tipo `pid_t` representa el identificador de un proceso. En la biblioteca GNU C es un tipo entero con signo cuyo tamaño depende de la arquitectura del sistema.

## Grupos de procesos

Además de la relación padre–hijo, los procesos pueden organizarse en **grupos de procesos**, los cuales permiten al sistema operativo gestionar de forma conjunta conjuntos de procesos relacionados, por ejemplo, los asociados a una misma sesión o terminal.

Para identificar el grupo al que pertenece un proceso:

```c
#include <sys/types.h>
#include <unistd.h>

pid_t getpgrp(void);  /* Retorna el PGID del proceso actual */
pid_t setpgrp(void);  /* Establece el proceso como líder de un nuevo grupo */
```

`setpgrp()` establece el PID del proceso como su PGID, convirtiéndolo en líder del grupo. En implementaciones modernas, esta operación suele estar respaldada por `setpgid()`.

## Procesos huérfanos

Si un proceso padre termina su ejecución antes que sus procesos hijos, estos no quedan sin control. El kernel reasigna automáticamente a los procesos huérfanos al proceso con PID 1, que históricamente fue `init` y en sistemas GNU/Linux modernos suele corresponder a `systemd`. Este proceso se encarga de adoptar a los hijos y recoger su estado de terminación.

## Descriptores de archivo estándar

El uso de `stdout` pone de manifiesto una convención fundamental en UNIX: a cada proceso se le asocian por defecto tres descriptores de archivo estándar:

- **Entrada estándar** (`stdin`): descriptor 0, asociado típicamente al teclado.
- **Salida estándar** (`stdout`): descriptor 1, asociado normalmente a la pantalla.
- **Error estándar** (`stderr`): descriptor 2, asociado también a la pantalla, pero separado de la salida estándar.

Estas constantes están definidas en `<unistd.h>` como `STDIN_FILENO`, `STDOUT_FILENO` y `STDERR_FILENO`. Cuando se ejecuta `fork()`, el proceso hijo hereda los descriptores abiertos del padre, por lo que ambos pueden imprimir en la terminal sin necesidad de abrir explícitamente un archivo.

## Ejemplo 1: imprimir PID del padre y del hijo

Tras la ejecución de `fork()`, tanto el padre como el hijo imprimen sus respectivos identificadores:

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
    pid_t hijo;
    hijo = fork();

    if (hijo == 0) {
        /* Código ejecutado por el proceso hijo */
        fprintf(stdout, "Soy el hijo, PID=%ld\n", (long)getpid());
    } else if (hijo > 0) {
        /* Código ejecutado por el proceso padre */
        fprintf(stdout, "Soy el padre, PID=%ld\n", (long)getpid());
    } else {
        /* Error al crear el proceso */
        perror("fork");
        return EXIT_FAILURE;
    }
    return EXIT_SUCCESS;
}
```

## Ejemplo 2: cadena lineal de procesos

En una cadena de procesos, cada proceso crea un solo hijo y el padre deja de crear nuevos procesos. El resultado es una estructura lineal: P0 → P1 → P2 → P3 → ... → Pn

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>

int main(void) {
    pid_t hijo;
    int n = 5;

    for (int i = 0; i < n; i++) {
        hijo = fork();
        if (hijo > 0) {
            /* El padre deja de crear más procesos */
            break;
        }
        fprintf(stderr, "Proceso PID=%ld, PPID=%ld\n",
                (long)getpid(), (long)getppid());
    }
    return EXIT_SUCCESS;
}
```

En cada iteración, solo el proceso hijo continúa el ciclo. El padre sale con `break`, generando una cadena lineal donde cada proceso tiene exactamente un hijo, excepto el último.

## Ejemplo 3: abanico de procesos

En un abanico de procesos, un único proceso padre crea varios hijos,
pero los hijos no crean más procesos:

![forkaba](/assets/image_copy.png)
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
int main(void) {
 pid_t hijo;
 int n = 5;
 for (int i = 0; i < n; i++) {
 hijo = fork();
 if (hijo == 0) {
 /* El hijo no crea más procesos */
 break;
 }
 }
 fprintf(stderr, "Proceso PID=%ld, PPID=%ld\n", (long)getpid(), (long)getppid());
 return EXIT_SUCCESS;
}
```
 Solo el proceso padre ejecuta el ciclo completo. Cada iteración crea un `hijo` nuevo. Cada hijo ejecuta el `break` y no genera más procesos. Se obtiene una estructura tipo estrella (abanico).
## ¿Qué aprendí?

Aprendí que `getpid()` y `getppid()` son herramientas esenciales para trazar la jerarquía de procesos en un programa. Combinadas con `fork()`, permiten verificar en tiempo de ejecución qué proceso está ejecutando cada rama del código. También entendí la importancia de los grupos de procesos y cómo el sistema gestiona automáticamente a los procesos huérfanos mediante `init`/`systemd`.

## ¿Cómo podría mejorar esta práctica?

Podría extender el ejemplo de la cadena lineal para crear en su lugar un árbol de procesos, donde cada padre crea dos hijos en lugar de uno, y verificar la estructura resultante con `ps --forest` para visualizar la jerarquía completa.

## Salida en pantalla
![fork2](/assets/fork2.png)
*Ejemplo1.*

![fork3](/assets/fork3.png)
*Ejemplo2.*

![fork4](/assets/fork4.png)
*Ejemplo3.*