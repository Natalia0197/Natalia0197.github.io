---
layout: post
title: "3.3 Memoria Compartida"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

La forma más rápida de comunicar dos procesos es hacer que compartan una zona de memoria. A diferencia de los pipes o las colas de mensajes, la memoria compartida **no implica copias de datos** entre espacios de usuario y kernel: los procesos acceden directamente al mismo segmento de memoria.

## Funciones principales

### `shmget()` — Crear o acceder a un segmento

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
int shmget(key_t key, size_t size, int shmflg);
```

- `key`: llave generada con `ftok()`, o `IPC_PRIVATE`.
- `size`: tamaño en bytes del segmento a crear.
- `shmflg`: máscara de bits con `IPC_CREAT`, `IPC_EXCL` y permisos.

Retorna un identificador entero en caso de éxito, o `-1` en caso de error.

### `shmat()` y `shmdt()` — Unirse y separarse del segmento

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
char *shmat(int shmid, char *shmaddr, int shmflg);
int   shmdt(char *shmaddr);
```

`shmat()` une el segmento al espacio de direcciones del proceso y retorna la dirección inicial. Si `shmaddr` es `NULL`, el sistema selecciona la dirección automáticamente. `shmdt()` desune el segmento (no lo elimina).

### `shmctl()` — Controlar el segmento

```c
int shmctl(int shmid, int cmd, struct shmid_ds *buf);
```

Comandos más usados:

- `IPC_STAT`: lee el estado del segmento.
- `IPC_RMID`: marca el segmento para eliminación (se borra cuando todos los procesos lo desundan).

Para eliminar el segmento:

```c
shmctl(shmid, IPC_RMID, 0);
```

## Ejemplo: comunicación entre padre e hijo mediante memoria compartida

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/shm.h>
#include <sys/wait.h>
#include <unistd.h>
#define TAM 256

int main(void) {
    int shmid;
    char *memoria;
    pid_t hijo;

    /* Crear segmento de memoria compartida */
    if ((shmid = shmget(IPC_PRIVATE, TAM, IPC_CREAT | 0600)) == -1) {
        perror("shmget");
        exit(EXIT_FAILURE);
    }

    if ((hijo = fork()) == 0) {
        /* Hijo: escribe en la memoria compartida */
        memoria = shmat(shmid, NULL, 0);
        snprintf(memoria, TAM, "Mensaje del hijo, PID=%ld", (long)getpid());
        shmdt(memoria);
        exit(EXIT_SUCCESS);
    } else {
        /* Padre: espera al hijo y lee la memoria compartida */
        wait(NULL);
        memoria = shmat(shmid, NULL, 0);
        printf("Padre recibió: %s\n", memoria);
        shmdt(memoria);
        shmctl(shmid, IPC_RMID, 0); /* Eliminar el segmento */
    }
    return EXIT_SUCCESS;
}
```

## ¿Qué aprendí?

Aprendí que la memoria compartida es el mecanismo IPC más eficiente para transferir grandes volúmenes de datos entre procesos, ya que evita copias innecesarias. Sin embargo, requiere sincronización explícita (con semáforos o mutex) para evitar condiciones de carrera cuando varios procesos acceden al mismo segmento simultáneamente.

## ¿Cómo podría mejorar esta práctica?

Podría combinar la memoria compartida con un semáforo de System V para proteger el acceso al segmento, implementando así un productor-consumidor donde el padre produce datos y el hijo los consume de forma sincronizada.

## Salida en pantalla

![memoria](/assets/mem1.png)
*Ejemplo1.*