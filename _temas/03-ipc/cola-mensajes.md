---
layout: post
title: "3.4 Cola de Mensajes"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

Las colas de mensajes permiten el intercambio de datos con un formato determinado entre procesos. A diferencia de los pipes, los mensajes tienen un **tipo** que permite al receptor seleccionar cuál mensaje leer, sin importar el orden de llegada.

## Funciones principales

### `msgget()` — Crear o acceder a una cola

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/msg.h>
int msgget(key_t key, int msgflg);
```

Si se coloca `IPC_PRIVATE` en `key`, se crea una nueva cola. Si se usa la llave de `ftok()`, se debe incluir `IPC_CREAT` en `msgflg` junto con los permisos.

### `msgsnd()` y `msgrcv()` — Enviar y recibir mensajes

```c
int    msgsnd(int msqid, const void *msgp, size_t msgsz, int msgflg);
ssize_t msgrcv(int msqid, void *msgp, size_t msgsz, long msgtyp, int msgflg);
```

Los mensajes se estructuran con `msgbuf`:

```c
struct msgbuf {
    long mtype;    /* Tipo de mensaje, debe ser > 0 */
    char mtext[i]; /* Datos del mensaje de longitud i */
};
```

Para `msgrcv()`, el parámetro `msgtyp` permite seleccionar el mensaje a leer:

- `0`: lee el primer mensaje en la cola.
- `> 0`: lee el primer mensaje de ese tipo.
- `< 0`: lee el primer mensaje con tipo menor o igual al valor absoluto de `msgtyp`.

### `msgctl()` — Controlar la cola

```c
int msgctl(int msqid, int cmd, struct msqid_ds *buf);
```

Para eliminar la cola: `msgctl(msgid, IPC_RMID, NULL)`.

## Ejemplo: enviar y recibir mensajes con la hora del sistema

```c
/* Compilar: gcc -Wall mcola.c -o mcola
   Enviar:   ./mcola s
   Recibir:  ./mcola r */

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <unistd.h>
#include <errno.h>
#include <sys/msg.h>

struct msgbuf {
    long mtype;
    char mtext[80];
};

void send_msg(int qid, int msgtype) {
    struct msgbuf msg;
    time_t t;
    msg.mtype = msgtype;
    time(&t);
    snprintf(msg.mtext, sizeof(msg.mtext), "El mensaje salió el: %s", ctime(&t));
    if (msgsnd(qid, (void *)&msg, sizeof(msg.mtext), IPC_NOWAIT) == -1) {
        perror("ERROR en msgsnd");
        exit(EXIT_FAILURE);
    }
    printf("Mensaje enviado: %s\n", msg.mtext);
}

void get_msg(int qid, int msgtype) {
    struct msgbuf msg;
    if (msgrcv(qid, (void *)&msg, sizeof(msg.mtext), msgtype,
               MSG_NOERROR | IPC_NOWAIT) == -1) {
        if (errno != ENOMSG) {
            perror("ERROR en msgrcv");
            exit(EXIT_FAILURE);
        }
        printf("No hay mensajes disponibles.\n");
    } else {
        printf("Mensaje recibido: %s\n", msg.mtext);
    }
}

int main(int argc, char *argv[]) {
    int qid, modo = 0, msgtype = 1;
    key_t llave;

    if (argc < 2) {
        printf("Uso: ./mcola s|r\n");
        exit(EXIT_FAILURE);
    }

    llave = ftok(argv[0], 'a');
    if (strcmp(argv[1], "s") == 0) modo = 1;
    else if (strcmp(argv[1], "r") == 0) modo = 2;
    else { printf("Uso: ./mcola s|r\n"); exit(EXIT_FAILURE); }

    if ((qid = msgget(llave, IPC_CREAT | 0666)) == -1) {
        perror("msgget");
        exit(EXIT_FAILURE);
    }

    if (modo == 2) get_msg(qid, msgtype);
    else send_msg(qid, msgtype);

    return EXIT_SUCCESS;
}
```
## Ejemplo 2: Cola de Mensajes (usuarios conectados)

```c
#define _GNU_SOURCE
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <utmp.h>
#include <sys/ipc.h>
#include <sys/msg.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>

#define MSG_TYPE  1
#define MAX_TEXT  256

/*Estructura del mensaje en la cola */
typedef struct {
    long tipo;                  // requerido por msgrcv/msgsnd  
    char usuario[UT_NAMESIZE];  // ut_user (32 chars max)        
    time_t timestamp;           // ut_tv.tv_sec                  
} Mensaje;

/* 
   PRODUCTOR (hijo): lee /var/run/utmp == who
   y envía cada sesión activa a la cola
*/
void productor(int mqid) {
    struct utmp *entrada;

    setutent();   // abre /var/run/utmp al inicio 

    while ((entrada = getutent()) != NULL) {
        /* Solo nos interesan sesiones de usuario activas */
        if (entrada->ut_type != USER_PROCESS) continue;

        Mensaje msg;
        memset(&msg, 0, sizeof(msg));
        msg.tipo = MSG_TYPE;
        strncpy(msg.usuario,  entrada->ut_user, UT_NAMESIZE - 1);
        msg.timestamp = (time_t) entrada->ut_tv.tv_sec;

        if (msgsnd(mqid, &msg, sizeof(msg) - sizeof(long), 0) == -1) {
            perror("msgsnd");
            exit(1);
        }
    }

    endutent();   // cierra el archivo utmp 

    Mensaje fin;
    memset(&fin, 0, sizeof(fin));
    fin.tipo = MSG_TYPE;
    msgsnd(mqid, &fin, sizeof(fin) - sizeof(long), 0);

    exit(0);
}

/*
   CONSUMIDOR (padre): recibe mensajes
   y los imprime con ctime() 
*/
void consumidor(int mqid) {
    Mensaje msg;

    while (1) {
        if (msgrcv(mqid, &msg, sizeof(msg) - sizeof(long), MSG_TYPE, 0) == -1) {
            perror("msgrcv");
            break;
        }

        if (msg.usuario[0] == '\0') break;

        char *hora = ctime(&msg.timestamp);
        if (hora) hora[strcspn(hora, "\n")] = '\0';

        printf("%s conectado %s\n", hora, msg.usuario);
    }
}

int main(void) {
    /* 1. Crear cola de mensajes */
    key_t key = ftok("/tmp", 'W');   // clave basada en archivo + id
    if (key == -1) { perror("ftok"); exit(1); }

    int mqid = msgget(key, IPC_CREAT | 0666);
    if (mqid == -1) { perror("msgget"); exit(1); }

    /* 2. fork: hijo = productor, padre = consumidor */
    pid_t pid = fork();
    if (pid < 0) { perror("fork"); exit(1); }

    if (pid == 0) {
        productor(mqid);   // hijo envía mensajes y sale
    } else {
        wait(NULL);        // espera a que el hijo termine de leer utmp
        consumidor(mqid);  // padre consume e imprime

        /* 3. Eliminar la cola al terminar */
        if (msgctl(mqid, IPC_RMID, NULL) == -1)
            perror("msgctl IPC_RMID");
    }

    return 0;
}
```
## ¿Qué aprendí?

Aprendí que las colas de mensajes son más flexibles que los pipes porque permiten seleccionar mensajes por tipo, lo que hace posible implementar protocolos donde diferentes tipos de mensajes tienen diferentes prioridades o destinatarios. Además, los mensajes persisten en la cola hasta que son leídos, a diferencia de los pipes que bloquean al escribir si el buffer está lleno.

## ¿Cómo podría mejorar esta práctica?

Podría usar múltiples tipos de mensajes para implementar un sistema de comunicación donde el padre envía tareas de distintas prioridades y los hijos solo leen los mensajes del tipo que les corresponde, demostrando el filtrado por `msgtyp`.

## Salida en pantalla

![cola](/assets/cola1.png)
*Ejemplo1.*

![cola2](/assets/cola2.png)
*Ejemplo2.*