---
layout: post
title: "7.3.1 Funciones setjmp y longjmp"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

Las funciones `setjmp()` y `longjmp()` permiten realizar **saltos no locales** en la ejecución de un programa, lo que significa transferir el control hacia un punto previo del código sin usar `return`. Son especialmente útiles dentro de rutinas de tratamiento de señales.

**Prototipos:**
```c
#include <setjmp.h>
int setjmp(jmp_buf env);
void longjmp(jmp_buf env, int val);

// Versiones que guardan/restauran la máscara de señales:
int sigsetjmp(sigjmp_buf env, int savesigs);
void siglongjmp(sigjmp_buf env, int val);
```

**Funcionamiento:**
- `setjmp()` guarda el entorno de ejecución en `env` (puntero de pila, registro de instrucción, registros, máscara de señal) y retorna `0` la primera vez.
- `longjmp()` restaura ese entorno, haciendo que `setjmp()` "retorne" nuevamente, pero esta vez con el valor `val` (si `val == 0`, retorna `1`).

**Ejemplo:**

```c
#include <signal.h>
#include <setjmp.h>
#include <stdio.h>

jmp_buf env;

void sigusr1_handler(int sig) {
    signal(SIGUSR1, sigusr1_handler);
    longjmp(env, 1); // regresar al punto del setjmp
}

int main() {
    int i;
    signal(SIGUSR1, sigusr1_handler);
    for (i = 0; i < 10; i++) {
        if (setjmp(env) == 0)
            printf("Punto a regresar en estado %d\n", i);
        else
            printf("Regreso al estado %d\n", i);
        sleep(10);
    }
    return EXIT_SUCCESS;
}
```

Para probar:
```bash
./retorno &
[1] 12212
Punto a regresar en estado 0

kill -10 12212   # enviar SIGUSR1
Regreso al estado 0
```

## ¿Cómo podría mejorar esta práctica?

Explorar la diferencia entre `setjmp/longjmp` (que pueden no guardar la máscara de señales) y `sigsetjmp/siglongjmp` (que guardan y restauran la máscara). Usar `sigsetjmp` es más seguro en manejadores de señales.

## Salida en pantalla

```
Punto a regresar en estado 0
Regreso al estado 0
Punto a regresar en estado 0
Regreso al estado 0
...
Punto a regresar en estado 9
```