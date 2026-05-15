---
layout: post
title: "3.1.1 Tuberías sin Nombre — pipe()"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

Las tuberías sin nombre, también llamadas **pipe**, son la forma más antigua de IPC y están disponibles en todos los sistemas UNIX y derivados. Se crean llamando a la función `pipe()`, cuyo prototipo es:

```c
#include <unistd.h>
int pipe(int filedes[2]);
```

La función retorna `0` si todo está correcto y `-1` si existe un error. Los dos descriptores se retornan a través del argumento `filedes`:

- `filedes[0]`: se utiliza para **leer** de la tubería.
- `filedes[1]`: se utiliza para **escribir** en la tubería.

Normalmente la salida de `filedes[1]` es la entrada para `filedes[0]`. Cuando se quiere comunicar padre e hijo para que ambos lean y escriban, se crea la tubería antes del `fork()` y cada proceso cierra el extremo que no usa.

## Ejemplo 1: el padre escribe, el hijo lee

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>
#define MAXLINEA 80

int main() {
    int n, fd[2];
    pid_t hijo;
    char linea[MAXLINEA];

    if (pipe(fd) < 0) {
        fprintf(stderr, "error de pipe");
        exit(EXIT_FAILURE);
    }
    if ((hijo = fork()) < 0) {
        fprintf(stderr, "error de fork");
        exit(EXIT_FAILURE);
    } else if (hijo > 0) {
        /* Padre: escribe en la tubería */
        close(fd[0]);
        write(fd[1], "hola mundo\n", 12);
    } else {
        /* Hijo: lee de la tubería */
        close(fd[1]);
        n = read(fd[0], linea, MAXLINEA);
        write(STDOUT_FILENO, linea, n);
    }
    return EXIT_SUCCESS;
}
```

## Ejemplo 2: el hijo escribe, el padre lee

```c
#include <unistd.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <stdio.h>
#define MAXLINE 80

int main() {
    int n, fd[2];
    pid_t hijo;
    char linea[MAXLINE];

    if (pipe(fd) < 0) {
        fprintf(stderr, "error de tubería");
        exit(EXIT_FAILURE);
    }
    if ((hijo = fork()) == 0) {
        /* Hijo: escribe en la tubería */
        close(fd[0]);
        write(fd[1], "hola mundo\n", 12);
    } else {
        /* Padre: lee de la tubería */
        close(fd[1]);
        n = read(fd[0], linea, MAXLINE);
        write(STDOUT_FILENO, linea, n);
        printf("numero de bytes leidos: %d\n", n);
    }
    return EXIT_SUCCESS;
}
```

## Ejemplo 3: Factorial de dos números 
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
#include <stdint.h>


long double factorial(int n) {
    if (n <= 1) return 1; // Caso base: 0! y 1! son 1
    return n * factorial(n - 1); // Multiplicación recursiva
}
int main(int argc, char *argv[])
{
    int x,aux,fd1[2],fd2[2];  // 0 es para lectura y 1 para escritura
    long double resultado1,resultado2;
    pid_t fibo;
    if (pipe(fd1) == -1){ // se manda un array de 2 para referenciar los descriptores de archivos
        perror("pipe1");
        exit(EXIT_FAILURE);
    }if (pipe(fd2) == -1){
        perror("pipe2");
        exit(EXIT_FAILURE);
    }
    fibo = fork();

        if (fibo > 0)
        {
            close(fd1[0]);
            close(fd2[1]);
            x=atoi(argv[2]);
            write(fd1[1],&x,sizeof(int));
            printf("Valor escrito %d\n",x);
            resultado1 = factorial(atoi(argv[1]));
            printf("El factorial de %d es: %Lf\n",atoi(argv[1]),resultado1);
            wait(NULL);
            read(fd2[0],&resultado2,sizeof(long double)); // READ devuelve la cantidad de bytes leidos pero el dato se guarda en el buffer
            printf("El factorial de %d es: %Lf\n",atoi(argv[2]),resultado2);
        }else{
            close(fd1[1]);
            close(fd2[0]);
            read(fd1[0],&aux,sizeof(int));
            printf("Valor leido: %d\n",aux);
            resultado2 = factorial(aux);
            write(fd2[1],&resultado2,sizeof(long double));
        }
    
    return EXIT_SUCCESS;
}
```

## ¿Qué aprendí?

Aprendí que `pipe()` crea un canal unidireccional de comunicación entre procesos. La clave está en cerrar el extremo que no se usa en cada proceso: si el padre escribe, debe cerrar `fd[0]`, y el hijo que lee debe cerrar `fd[1]`. No hacerlo puede dejar descriptores abiertos que impidan que `read()` retorne al detectar fin de archivo.

## ¿Cómo podría mejorar esta práctica?

Podría usar `dup2()` para redirigir la salida estándar del hijo hacia el extremo de escritura de la tubería, lo que permitiría que el hijo use `printf()` normalmente y el padre lea su salida como si fuera una captura de pantalla.

## Salida en pantalla

![pipe1](/assets/pipe1.png)
*Ejemplo1.*

![pipe2](/assets/pipe2.png)
*Ejemplo2.*

![pipe3](/assets/pipe3.png)
*Ejemplo3.*