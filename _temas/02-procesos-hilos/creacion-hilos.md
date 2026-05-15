---
layout: post
title: "2.8.2 Creación de Hilos"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

La función `pthread_create()` se usa para crear un hilo con ciertos atributos, el cual ejecutará una función determinada con los argumentos indicados. Su prototipo es:

```c
#include <pthread.h>
int pthread_create(pthread_t *thread, const pthread_attr_t *attr,
                   void *(*start_routine)(void *), void *arg);
```

Los atributos del hilo se especifican en el parámetro `attr`. Si `attr` es `NULL`, se usan los atributos por omisión. Si la función se realiza con éxito, el ID del hilo queda almacenado en la localidad referenciada por `thread`. El hilo creado ejecuta `start_routine()` con `arg` como argumento. Si se necesita pasar más de un parámetro, se puede crear una estructura con los campos necesarios.

`pthread_create()` retorna `0` en caso de éxito, o un número de error en otro caso. Los errores posibles son:

- `EAGAIN`: recursos insuficientes para crear un hilo.
- `EINVAL`: atributo inválido.
- `EPERM`: política de planificación no permitida.

## Terminación de un hilo

Un hilo termina cuando:

- Se llama a `pthread_exit()` especificando un valor de estado final.
- Realiza un `return` al final de `start_routine()`.
- Es cancelado invocando `pthread_cancel()`.
- El hilo principal ejecuta `return` desde `main()`.

## Esperar la terminación: `pthread_join()`

La función `pthread_join()` suspende la ejecución del hilo que hace la llamada hasta que el hilo destino termine, funcionando de manera similar a `wait()` para procesos. Su prototipo es:

```c
#include <pthread.h>
int pthread_join(pthread_t thread, void **value_ptr);
```

## Ejemplo: factorial con un hilo

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

long prod = 1;
void *factorial(void *valor);

int main(int argc, char *argv[]) {
    pthread_t tid;
    pthread_attr_t attr;

    if (argc != 2) {
        fprintf(stderr, "Uso: ./hilos <entero>\n");
        return EXIT_FAILURE;
    }

    pthread_attr_init(&attr);
    pthread_create(&tid, &attr, factorial, argv[1]);
    pthread_join(tid, NULL); /* Esperar finalización del hilo */

    printf("Factorial: %ld\n", prod);
    return EXIT_SUCCESS;
}

void *factorial(void *valor) {
    int i = 1;
    while (i <= atol(valor))
        prod *= (i++);
    pthread_exit(0);
}
```

## Ejemplo: múltiples hilos con estructura de datos

```c
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

typedef struct dhilos {
    int id;
    long prod;
} DHILOS;

DHILOS pm_hilos[10];
void *factorial(void *valor);

int main(int argc, char *argv[]) {
    pthread_t tid[argc - 1];
    pthread_attr_t attr;
    int i;

    if (argc < 2) {
        perror("Uso: ./chilos <entero1> <entero2> ...\n");
        exit(EXIT_FAILURE);
    }

    for (i = 0; i < argc - 1; i++) {
        pthread_attr_init(&attr);
        pm_hilos[i].id = i + 1;
        pm_hilos[i].prod = atol(argv[i + 1]);
        pthread_create(&tid[i], &attr, factorial, &pm_hilos[i]);
    }

    for (i = 0; i < argc - 1; i++) {
        pthread_join(tid[i], NULL);
        printf("Hilo %d: factorial = %ld\n", pm_hilos[i].id, pm_hilos[i].prod);
    }
    return EXIT_SUCCESS;
}

void *factorial(void *valor) {
    DHILOS *datos = (DHILOS *)valor;
    long n = datos->prod;
    long result = 1;
    for (long i = 1; i <= n; i++)
        result *= i;
    datos->prod = result;
    pthread_exit(0);
}
```

Compilar con:

```bash
gcc -Wall chilos.c -lpthread -o chilos
```

## ¿Qué aprendí?

Aprendí que `pthread_create()` es la función central para la programación concurrente con hilos en C. A diferencia de `fork()`, los hilos comparten el mismo espacio de memoria, lo que facilita el intercambio de datos pero también requiere sincronización para evitar condiciones de carrera. El uso de estructuras como `DHILOS` permite pasar múltiples argumentos a un hilo de forma limpia.

## ¿Cómo podría mejorar esta práctica?

Podría agregar un mutex para proteger la variable `prod` cuando múltiples hilos la modifican simultáneamente, demostrando así la importancia de la sincronización en programación concurrente.

## Salida en pantalla

![hilos1](/assets/hilos1.png)
*Ejemplo1.*

![hilos2](/assets/hilos2.png)
*Ejemplo2.*