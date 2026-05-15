---
layout: post
title: "7.1 Introduccion"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

Las **señales** son interrupciones de software que se envían a un proceso para informarle de un evento asíncrono o situación especial. El sistema operativo identifica cada señal con un número entero positivo y un nombre que comienza con las letras `SIG`.

Cuando un proceso recibe una señal, puede reaccionar de **tres formas**:

1. **Ignorar la señal**: el proceso es inmune a ella (siempre que tenga mayor prioridad que el proceso que la envía).

2. **Invocar la rutina de tratamiento por defecto**: aportada por el kernel. Generalmente provoca la terminación del proceso via `exit()`. Algunas señales además generan un archivo `core` con el volcado de memoria del proceso, útil para depuración.

3. **Invocar una rutina propia**: el programador define el comportamiento mediante un manejador personalizado.

Los procesos pueden enviarse señales entre sí usando la llamada al sistema `kill()`:

```c
#include <sys/types.h>
#include <signal.h>
int kill(pid_t pid, int sig);
```

Donde `pid` identifica el proceso destino (o grupo de procesos), y `sig` es el número de señal.

Un proceso también puede enviarse señales a sí mismo con `raise()`:
```c
#include <signal.h>
int raise(int sig);
```
