---
layout: post
title: "3.2.2 Semáforos en System V"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

Los semáforos son mecanismos utilizados por los sistemas para sincronizar el acceso a recursos compartidos. En los sistemas derivados de System V, los semáforos se implementan en el kernel para garantizar que las operaciones de incremento y decremento sean **atómicas**.

En System V un semáforo no es un simple valor, sino un **conjunto de valores enteros no negativos**, donde cada valor puede asumir cualquier número no negativo hasta un máximo definido por el sistema.

## Funciones principales

### `semget()` — Crear o acceder a un conjunto de semáforos

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
int semget(key_t key, int nsems, int semflg);
```

Retorna el identificador del conjunto de semáforos en caso de éxito, o `-1` en caso de error. Los parámetros son:

- `key`: llave generada con `ftok()`, o `IPC_PRIVATE` para crear un semáforo privado.
- `nsems`: número de semáforos en el conjunto.
- `semflg`: máscara de bits con `IPC_CREAT`, `IPC_EXCL` y permisos (ej. `0600`).

### `semop()` — Operar sobre semáforos

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
int semop(int semid, struct sembuf *sops, size_t nsops);
```

Realiza operaciones atómicas sobre los semáforos. Cada operación se especifica con una estructura `sembuf`:

```c
struct sembuf {
    unsigned short sem_num;  /* número de semáforo en el conjunto */
    short sem_op;            /* operación: positivo=incrementar, negativo=decrementar, 0=esperar cero */
    short sem_flg;           /* banderas: IPC_NOWAIT, SEM_UNDO */
};
```

### `semctl()` — Controlar semáforos

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
int semctl(int semid, int semnum, int cmd, union semun arg);
```

Permite inicializar, consultar, modificar y eliminar semáforos. Comandos más usados:

- `SETVAL`: inicializa un semáforo a un valor determinado.
- `GETVAL`: lee el valor de un semáforo.
- `IPC_RMID`: elimina el conjunto de semáforos.

## Ejemplo: sincronización padre-hijo con semáforos

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <unistd.h>
#define SEM_HIJO  0
#define SEM_PADRE 1

int main(int argc, char *argv[]) {
    int i = 5, semid;
    pid_t pid;
    struct sembuf operacion;
    key_t llave;

    llave = ftok(argv[0], 'a');
    if ((semid = semget(llave, 2, IPC_CREAT | 0600)) == -1) {
        perror("semget");
        exit(EXIT_FAILURE);
    }

    semctl(semid, SEM_HIJO,  SETVAL, 0); /* semáforo hijo cerrado  */
    semctl(semid, SEM_PADRE, SETVAL, 1); /* semáforo padre abierto */

    if ((pid = fork()) == -1) {
        perror("fork");
        exit(EXIT_FAILURE);
    } else if (pid == 0) {
        /* Proceso hijo */
        while (i) {
            operacion.sem_num = SEM_HIJO;
            operacion.sem_op  = -1;
            operacion.sem_flg = 0;
            semop(semid, &operacion, 1);

            printf("Proceso hijo: %d\n", i--);

            operacion.sem_num = SEM_PADRE;
            operacion.sem_op  = 1;
            semop(semid, &operacion, 1);
        }
        semctl(semid, 0, IPC_RMID, 0);
    } else {
        /* Proceso padre */
        while (i) {
            operacion.sem_num = SEM_PADRE;
            operacion.sem_op  = -1;
            operacion.sem_flg = 0;
            semop(semid, &operacion, 1);

            printf("Proceso padre: %d\n", i--);

            operacion.sem_num = SEM_HIJO;
            operacion.sem_op  = 1;
            semop(semid, &operacion, 1);
        }
        semctl(semid, 0, IPC_RMID, 0);
    }
    return EXIT_SUCCESS;
}
```
## Ejemplo2: Semáforos para exclusión mutúa


```c
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <semaphore.h>
#include <sys/mman.h>
#include <sys/wait.h>
#include <crypt.h>
#include <time.h>

#define PASS_LEN 11

typedef struct {
    char perm[PASS_LEN + 1];
    int  found;
    int  done;
} SharedMem;

typedef struct {
    sem_t mutex;
    sem_t listo;
} Sems;

/* Hash real de /etc/shadow */
const char *HASH_ORIGINAL =
    //"$y$j9T$U3m1aA2sioWSWkh3GCYKQ/$dGcsiTqsX2Y8RgMpJESYB1hCu.sVUN0et.897MLLoj4";
    "$y$j9T$QXaYnGfPtA3cgshAEKDkk0$F/VochlwTN1yhlxAhhQ9G/h4RDKV6Pjjs7CchNXgp/6";

void swap_c(char *a, char *b) { char t = *a; *a = *b; *b = t; }

void permute(char *arr, int n, SharedMem *shm, Sems *sems) {
    if (shm->found) return;
    if (n == 1) {
        sem_wait(&sems->mutex);
        strncpy(shm->perm, arr, PASS_LEN);
        shm->perm[PASS_LEN] = '\0';
        sem_post(&sems->listo);
        return;
    }
    for (int i = 0; i < n; i++) {
        if (shm->found) return;
        permute(arr, n - 1, shm, sems);
        if (n % 2 == 0) swap_c(&arr[i], &arr[n-1]);
        else             swap_c(&arr[0], &arr[n-1]);
    }
}

void proceso_hijo(SharedMem *shm, Sems *sems) {
    char local_perm[PASS_LEN + 1];
    time_t inicio, fin;
    double segundos;
    time(&inicio);
    printf("Cronómetro iniciado...\n");
    while (1) {
        sem_wait(&sems->listo);

        strncpy(local_perm, shm->perm, PASS_LEN);
        local_perm[PASS_LEN] = '\0';
        int done = shm->done;
        sem_post(&sems->mutex);

        if (done) {
            printf("[hijo] Contraseña no encontrada.\n");
            exit(1);
        }

        /* crypt() recibe la permutación y el hash completo como salt.
           Extrae el algoritmo ($y$) y el salt automáticamente.       */
        char *resultado = crypt(local_perm, HASH_ORIGINAL);

        printf("[hijo] Probando: %s\n", local_perm);

        if (strcmp(resultado, HASH_ORIGINAL) == 0) {
            shm->found = 1;
            time(&fin);
            segundos = difftime(fin, inicio);
            printf("\n[hijo] ¡ÉXITO! Contraseña encontrada: %s\n", local_perm);
            printf("Han transcurrido %.2f minutos.\n", segundos/60);
            exit(0);
            

        }
    }
}

int main(void) {
    /* La contraseña de arranque para las permutaciones.
       Mismos 8 chars que fueron usados al crear el usuario. */
    //char arr[PASS_LEN + 1] = "lmopezc7";
    
    char arr[PASS_LEN + 1] = "milly+56890";
    

    SharedMem *shm = mmap(NULL, sizeof(SharedMem),
                          PROT_READ | PROT_WRITE,
                          MAP_SHARED | MAP_ANONYMOUS, -1, 0);
    shm->found = 0; shm->done = 0;

    Sems *sems = mmap(NULL, sizeof(Sems),
                      PROT_READ | PROT_WRITE,
                      MAP_SHARED | MAP_ANONYMOUS, -1, 0);
    sem_init(&sems->mutex, 1, 1);
    sem_init(&sems->listo, 1, 0);

    pid_t pid = fork();
    if (pid < 0) { perror("fork"); exit(1); }

    if (pid == 0) {
        proceso_hijo(shm, sems);
    } else {
        permute(arr, PASS_LEN, shm, sems);

        if (!shm->found) {
            sem_wait(&sems->mutex);
            shm->done = 1;
            sem_post(&sems->listo);
        }

        wait(NULL);
        sem_destroy(&sems->mutex);
        sem_destroy(&sems->listo);
        munmap(shm,  sizeof(SharedMem));
        munmap(sems, sizeof(Sems));
    }
    return 0;
}
```

## ¿Qué aprendí?

Aprendí que los semáforos de System V son más complejos que los POSIX, pero ofrecen mayor control al trabajar con conjuntos de semáforos en lugar de uno individual. La clave es entender el patrón: decrementar (`sem_op = -1`) para entrar a la sección crítica y bloquear si ya está en uso, e incrementar (`sem_op = 1`) al salir para despertar al proceso que espera.

## ¿Cómo podría mejorar esta práctica?

Podría agregar un manejador de señales para `SIGINT` que llame a `semctl(semid, 0, IPC_RMID, 0)` antes de terminar, garantizando que el semáforo sea eliminado aunque el programa se interrumpa con Ctrl+C, evitando así recursos IPC huérfanos en el sistema.

## Salida en pantalla


![semaforo](/assets/sem1.png)
*Ejemplo1.*

![semaforo2](/assets/semaforo.png)
*Ejemplo2.*