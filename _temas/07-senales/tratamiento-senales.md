---
layout: post
title: "7.3 Tratamiento de Senales"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

Para especificar qué acción tomar cuando se recibe una señal, se usa la función `signal()`:

```c
#include <signal.h>
typedef void (*sighandler_t)(int);
sighandler_t signal(int signum, sighandler_t handler);
```

El parámetro `handler` puede ser:
- `SIG_DFL`: ejecutar la acción por defecto del kernel
- `SIG_IGN`: ignorar la señal
- **Dirección de una función**: rutina personalizada del programador

La rutina personalizada debe tener el prototipo:
```c
void handler(int sig [, int code, struct sigcontext *scp]);
```

La llamada al manejador es **asíncrona**: puede ocurrir en cualquier momento durante la ejecución del programa.

**Ejemplo — capturar SIGINT (Ctrl+C):**

```c
#include <stdio.h>
#include <signal.h>
#include <unistd.h>

void sigint_handler(int sig) {
    static int cont = 0;
    printf("Señal %d recibida. Contador = %d\n", sig, cont++);
    if (cont >= 20) exit(EXIT_SUCCESS);
    signal(SIGINT, sigint_handler); // reinstalar el manejador
}

int main() {
    signal(SIGINT, sigint_handler);
    while (1) {
        printf("En espera de Ctrl+C\n");
        sleep(99);
    }
    return EXIT_SUCCESS;
}
```

Este programa solo termina después de recibir 20 veces la señal `SIGINT`. También se puede enviar la señal por comando:

```bash
./controlC &        # lanzar en background
kill -2 [pid]       # enviar SIGINT manualmente
```

## ¿Cómo podría mejorar esta práctica?

Comparar `signal()` con `sigaction()`, que ofrece mayor control y comportamiento más predecible entre distintas versiones de UNIX/Linux. `sigaction()` permite especificar si la señal se bloquea durante su propio manejo y otras opciones avanzadas.

## Salida en pantalla

```
En espera de Ctrl+C
^C
Señal 2 recibida. Contador = 0
En espera de Ctrl+C
^C
Señal 2 recibida. Contador = 1
...
Señal 2 recibida. Contador = 19
```