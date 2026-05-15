---
layout: post
title: "2.8 Hilos"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Los hilos o *threads* representan un mecanismo de ejecución concurrente dentro de un mismo proceso, y constituyen una alternativa más ligera que la creación de procesos independientes. A diferencia de los procesos, los hilos comparten el mismo espacio de direcciones, los mismos archivos abiertos y otros recursos del proceso al que pertenecen.

El uso de hilos resulta especialmente adecuado cuando se requiere:

- Ejecutar múltiples tareas de manera concurrente.
- Compartir datos de forma eficiente sin mecanismos costosos de IPC.
- Reducir el overhead asociado a la creación y destrucción de procesos.
- Mejorar la capacidad de respuesta de aplicaciones interactivas.
- Aprovechar arquitecturas multinúcleo.

## Modelo de hilos en POSIX

Para trabajar con hilos en C se utiliza la librería **pthreads**, que forma parte del estándar POSIX (IEEE POSIX 1003.1-2008). Las implementaciones que cumplen este estándar son conocidas como POSIX Threads o simplemente pthreads.

Cada hilo tiene su propio: contador de programa, stack y registros. Sin embargo, todos los hilos de un mismo proceso comparten el espacio de memoria, lo que los diferencia fundamentalmente de los procesos creados con `fork()`.

Para compilar programas que usen pthreads:

```bash
gcc hilos.c -o hilos -lpthread
```

## Principales funciones de pthreads

| Categoría | Función |
|---|---|
| Administración | `pthread_create`, `pthread_exit`, `pthread_kill`, `pthread_join`, `pthread_self` |
| Exclusión mutua | `pthread_mutex_init`, `pthread_mutex_lock`, `pthread_mutex_unlock` |
| Variables de condición | `pthread_cond_init`, `pthread_cond_wait`, `pthread_cond_signal` |