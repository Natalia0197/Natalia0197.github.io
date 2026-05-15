---
layout: post
title: "7.4 Funcion Alarma y Pausa"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

### Función `alarm()`

Permite configurar un **temporizador descendente**. Cuando el temporizador expira, el kernel envía la señal `SIGALRM` al proceso.

```c
#include <unistd.h>
unsigned int alarm(unsigned int seconds);
```

- Si `seconds = 0`, cancela cualquier alarma pendiente.
- Solo existe **una alarma activa por proceso**; llamar a `alarm()` nuevamente cancela la anterior.
- Retorna los segundos restantes de la alarma previa (o `0` si no había).

La señal `SIGALRM` puede capturarse o ignorarse. Si no se captura, su acción por defecto es **terminar el proceso**.

### Función `pause()`

Suspende el proceso hasta que se reciba y capture una señal:

```c
#include <unistd.h>
int pause(void);
```

Retorna `-1` cuando una señal es capturada y `errno` se fija en `EINTR`.

**Ejemplo — detener un contador con SIGALRM:**

```c
#include <signal.h>
#include <stdio.h>
#include <unistd.h>

#define SEG 2
int salir = 1;

void accion(int signum) {
    printf("\nRecibí señal: %d SIGALRM\n", signum);
    salir = 0;
}

int main() {
    int i = 0;
    printf("En %d segundos recibirás una alarma\n", SEG);
    signal(SIGALRM, accion);
    alarm(SEG);
    while (salir)
        printf("contemos: %d\n", i++);
    return EXIT_SUCCESS;
}
```

El programa cuenta hasta que el kernel envía `SIGALRM` después de 2 segundos, momento en que la función `accion()` cambia la bandera y detiene el ciclo.

## ¿Cómo podría mejorar esta práctica?

Combinar `alarm()` con `pause()` para implementar un temporizador más eficiente (sin desperdiciar CPU en un bucle activo). También explorar `setitimer()`, que ofrece mayor precisión que `alarm()`.

## Salida en pantalla

```
En 2 segundos recibirás una alarma
contemos: 0
contemos: 1
contemos: 2
...
contemos: 48392

Recibí señal: 14 SIGALRM
```