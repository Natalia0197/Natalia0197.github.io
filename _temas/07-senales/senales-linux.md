---
layout: post
title: "7.2.1 Senales en Linux"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

En Linux, las señales están definidas en `/usr/include/asm-generic/signal.h`. A diferencia de UNIX System V, Linux define más señales, incluyendo señales en tiempo real a partir de `SIGRTMIN (32)`.

La tabla completa de señales en Linux incluye:

| Nombre | Núm | Descripción |
|--------|-----|-------------|
| SIGHUP | 1 | Termina el proceso líder |
| SIGINT | 2 | Tecla Ctrl+C pulsada |
| SIGQUIT | 3 | Tecla Ctrl+\\ pulsada, termina terminal |
| SIGILL | 4 | Instrucción ilegal |
| SIGTRAP | 5 | Trazado de programas (depuradores) |
| SIGABRT/SIGIOT | 6 | Terminación anormal |
| SIGBUS | 7 | Error de bus |
| SIGFPE | 8 | Error aritmético, coma flotante |
| SIGKILL | 9 | Eliminar proceso incondicionalmente |
| SIGUSR1 | 10 | Señal definida por el usuario |
| SIGSEGV | 11 | Violación de segmento |
| SIGUSR2 | 12 | Señal definida por el usuario |
| SIGPIPE | 13 | Escritura en pipe sin lectores |
| SIGALRM | 14 | Fin del reloj ITIMER_REAL |
| SIGTERM | 15 | Señal de terminación de software |
| SIGCHLD | 17 | Hijo terminó (el kernel notifica al padre) |
| SIGCONT | 18 | Continuar proceso en segundo/primer plano |
| SIGSTOP | 19 | Suspensión de proceso (no ignorable) |
| SIGTSTP | 20 | Suspensión por Ctrl+Z |
| SIGRTMIN | 32 | Inicio de señales en tiempo real |

Para enviar señales con `kill()` desde C:

```c
#include <signal.h>
// pid > 0: enviar al proceso con ese PID
kill(1234, SIGTERM);

// pid = 0: enviar a todos los procesos del mismo grupo
kill(0, SIGUSR1);

// pid = -1: enviar a todos los procesos del usuario
kill(-1, SIGUSR2);
```

## ¿Cómo podría mejorar esta práctica?

Crear un programa que implemente comunicación entre procesos padre e hijo usando `SIGUSR1` y `SIGUSR2` para sincronizar acciones, en lugar de usar mecanismos de IPC más complejos.

## Salida en pantalla

```bash
$ cat /usr/include/asm-generic/signal.h | grep '#define SIG'
#define SIGHUP   1
#define SIGINT   2
#define SIGQUIT  3
...
#define SIGRTMIN 32
```