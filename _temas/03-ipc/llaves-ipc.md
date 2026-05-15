---
layout: post
title: "3.2.1 Llaves IPC con ftok()"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

Todas las formas de IPC de System V (excepto las tuberías sin nombre) tienen asociado un espacio de nombres para llevar a cabo el intercambio de mensajes. Este espacio de nombres se basa en una **llave** de tipo `key_t`, que es típicamente un entero de 32 bits.

Para crear llaves de forma reproducible, GNU/Linux proporciona la función `ftok()`, que utiliza el nombre de un archivo y un identificador de proyecto para generar una llave única. Su prototipo es:

```c
#include <sys/types.h>
#include <sys/ipc.h>
key_t ftok(const char *pathname, int proj_id);
```

El parámetro `pathname` es la ruta de un archivo ordinario existente y accesible. El entero `proj_id` se usa junto con `pathname` para generar la llave. En caso de error, la función retorna `-1`.

## ¿Cómo genera la llave `ftok()`?

La implementación combina tres valores para producir una llave única de 32 bits:

- Los 8 bits menos significativos de `proj_id`.
- El número de i-nodo del archivo especificado en `pathname`.
- El número menor del dispositivo del sistema de archivos donde reside el archivo.

## Uso típico

```c
#include <sys/types.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <stdio.h>
#include <stdlib.h>

int main(int argc, char *argv[]) {
    key_t llave;
    int semid;

    /* Genera la llave usando el nombre del ejecutable y el carácter 'a' */
    llave = ftok(argv[0], 'a');
    if (llave == -1) {
        perror("ftok");
        exit(EXIT_FAILURE);
    }

    /* Usa la llave para crear o acceder a un semáforo */
    semid = semget(llave, 1, IPC_CREAT | 0600);
    if (semid == -1) {
        perror("semget");
        exit(EXIT_FAILURE);
    }

    printf("Llave generada: %d, ID semáforo: %d\n", llave, semid);
    return EXIT_SUCCESS;
}
```

Se debe tener en consideración que los mecanismos que forman parte de un mismo proyecto deben compartir la misma llave. La misma llave puede usarse para acceder a semáforos con `semget()`, a memoria compartida con `shmget()`, y a colas de mensajes con `msgget()`.

## ¿Qué aprendí?

Aprendí que `ftok()` es el mecanismo estándar para que procesos independientes accedan al mismo recurso IPC sin necesidad de comunicarse su identificador de otra forma. La llave actúa como un nombre de acceso compartido: siempre que dos procesos usen el mismo archivo y el mismo `proj_id`, obtendrán la misma llave y podrán acceder al mismo recurso IPC.

## ¿Cómo podría mejorar esta práctica?

Podría verificar qué ocurre cuando el archivo referenciado por `ftok()` es eliminado y recreado: el i-nodo cambia, por lo que la llave generada sería diferente, lo que causaría que los procesos no pudieran acceder al mismo recurso IPC.

## Salida en pantalla

![ipc](/assets/ipc.png)
*Ejemplo1.*