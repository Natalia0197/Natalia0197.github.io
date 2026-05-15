---
layout: post
title: "3.1.2 Tuberías con Nombre — FIFO"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

La llamada al sistema `mkfifo()` permite crear un archivo especial llamado **FIFO**, que es una tubería con un nombre asociado en el sistema de archivos. A diferencia de `pipe()`, que crea un canal anónimo solo entre procesos emparentados, un FIFO puede ser utilizado por cualquier proceso que conozca su ruta. Su prototipo es:

```c
#include <sys/types.h>
#include <sys/stat.h>
int mkfifo(const char *pathname, mode_t mode);
```

El parámetro `pathname` es el nombre y ruta donde se creará el archivo FIFO, y `mode` especifica los permisos de dicho archivo. La función retorna `0` en caso de éxito y `-1` en caso de error.

Una vez creado el archivo FIFO, cualquier proceso puede abrirlo para lectura o escritura de la misma forma que un archivo ordinario. Sin embargo, **debe estar abierto en ambos extremos simultáneamente** antes de poder realizar operaciones de E/S. Abrir un FIFO para leer normalmente bloquea hasta que otro proceso lo abre para escribir, y viceversa.

## Ejemplo: comunicación padre-hijo mediante FIFO

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>

int main(void) {
    pid_t hijo;
    int file;
    char mensaje[20];

    unlink("namepipe");    /* Borra el archivo si ya existe */
    umask(~0666);          /* Cambia la máscara de permisos */

    if (mkfifo("namepipe", 0666) == -1) {
        perror("error en mkfifo");
        exit(EXIT_FAILURE);
    }

    if ((hijo = fork()) == 0) {
        /* Proceso hijo: escribe en el FIFO */
        fprintf(stdout, "soy el hijo, ID=%ld\n", (long)getpid());
        if ((file = open("namepipe", O_WRONLY)) == -1) {
            perror("error en open O_WRONLY");
            exit(EXIT_FAILURE);
        }
        write(file, "soy el hijo,ID...\n", 20);
        close(file);
        exit(EXIT_SUCCESS);
    }

    if (hijo > 0) {
        /* Proceso padre: lee del FIFO */
        fprintf(stdout, "soy el padre, ID=%ld\n", (long)getpid());
        if ((file = open("namepipe", O_RDONLY)) == -1) {
            perror("error en open O_RDONLY");
            exit(EXIT_FAILURE);
        }
        read(file, mensaje, 20);
        fprintf(stdout, "%s\n", mensaje);
        close(file);
    }
    return EXIT_SUCCESS;
}
```
## Ejemplo2: Tubería sin nombre Productor-Consumidor
```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/ipc.h>
#include <sys/sem.h>
#include <sys/shm.h>
#include <sys/wait.h>
#include <time.h>

#define MAX_COL 8

int col_matriz;
typedef struct
{
    int col;
    int datos[MAX_COL][MAX_COL];
} matriz_compartida;

/* estructura requerida por semctl */
union semun
{
    int val;
};

/* operaciones de semaforos */
// esperar
void P(int semid, int num)
{
    struct sembuf op;
    op.sem_num = num;
    op.sem_op = -1;
    op.sem_flg = 0;
    semop(semid, &op, 1);
}

// señal-continuar
void V(int semid, int num)
{
    struct sembuf op;
    op.sem_num = num;
    op.sem_op = 1;
    op.sem_flg = 0;
    semop(semid, &op, 1);
}
void generarMatriz(matriz_compartida *matriz)
{ // genera una matriz cuadrada
    int i, j;
    matriz->col =(rand() % 8) + 1;
    for (i = 0; i < matriz->col; i++)
    {
        for (j = 0; j < matriz->col; j++)
        {
            matriz->datos[i][j] = (rand() % 10); // numero aleatorio entre 0 y 9
        }
    }
    
}
void imprimir_matriz(matriz_compartida *matriz)
{
    int i, j;
    char c;
    for (i = 0; i < matriz->col; i++)
    {
        for (j = 0; j < matriz->col; j++)
        {
            c= matriz->datos[i][j]+'0';
            write(1, &c, 1); 
            write(1, " ", 1);
        }
        write(1, "\n", 1);
    }
}

// Función para obtener la submatriz (menor)
void obtenerSubmatriz(int matriz[8][8], int temporal[8][8], int fila, int col, int n) {
    int i = 0, j = 0;
    for (int r = 0; r < n; r++) {
        for (int c = 0; c < n; c++) {
            if (r != fila && c != col) {
                temporal[i][j++] = matriz[r][c];
                if (j == n - 1) {
                    j = 0;
                    i++;
                }
            }
        }
    }
}

long long calcularDeterminante(int matriz[8][8], int n) {
    long long det = 0;
    int temporal[8][8];
    int signo = 1;

    if (n == 1) return matriz[0][0];
    
    // Optimización para 2x2
    if (n == 2) return (long long)matriz[0][0] * matriz[1][1] - (long long)matriz[0][1] * matriz[1][0];

    for (int f = 0; f < n; f++) {
        obtenerSubmatriz(matriz, temporal, 0, f, n);
        det += (long long)signo * matriz[0][f] * calcularDeterminante(temporal, n - 1);
        signo = -signo;
    }

    return det;
}

int main(int argc, char *argv[])
{

    key_t llave;
    int semid, shmid,len;
    matriz_compartida *buffer;
    pid_t pid;
    union semun arg;
    long long det;
    char msg[32];
   
    srand(time(NULL)); // Semilla para el rand()

    /* generar clave */
    llave = ftok(argv[0], 51);

    /* crear memoria compartida */
    shmid = shmget(llave, sizeof(matriz_compartida), 0666 | IPC_CREAT);
    buffer = (matriz_compartida *)shmat(shmid, NULL, 0);

    /* crear 3 semáforos */
    semid = semget(llave, 3, 0666 | IPC_CREAT);

    /* inicializar semáforos */
    arg.val = 1;
    semctl(semid, 0, SETVAL, arg); // mutex: exclusion mutua

    arg.val = 1;
    semctl(semid, 1, SETVAL, arg); // empty: espacio disponible

    arg.val = 0;
    semctl(semid, 2, SETVAL, arg); // full:número de posiciones ocupadas

    pid = fork();

    if (pid == 0)
    {

        /* CONSUMIDOR */

        for (int i = 0; i < atoi(argv[1]); i++)
        {

            P(semid, 2); // esperar dato
            P(semid, 0); // entrar a sección crítica

            printf("Consumidor lee matriz %d:\n", i + 1); // retira de la sección
            det = calcularDeterminante(buffer->datos,buffer->col);
            imprimir_matriz(buffer);
            write(1,"El determinante es:",19);
            len = snprintf(msg,sizeof(msg),"%lld",det);
            write(1,msg,len);
            write(1,"\n",1);
            



            V(semid, 0); // liberar mutex: exclusion mutua
            V(semid, 1); // Señal - buffer vacío
            sleep(1);
        }
    }
    else
    {

        /* PRODUCTOR */

        for (int i = 1; i <= atoi(argv[1]); i++)
        { // producir

            P(semid, 1); // esperar la señal espacio
            P(semid, 0); // sección crítica

            generarMatriz(buffer);// le pasa el buffer para rellenarlo con los datos 
            printf("Productor produce matriz %d:\n", i);
            imprimir_matriz(buffer);

            V(semid, 0); // liberar sección critica - mutex
            V(semid, 2); // hay dato

            sleep(1);
        }

        wait(NULL);

        /* liberar recursos */
        shmdt(buffer);
        shmctl(shmid, IPC_RMID, NULL);
        semctl(semid, 0, IPC_RMID);
    }

    return EXIT_SUCCESS;
}
```
## ¿Qué aprendí?

Aprendí que los FIFOs resuelven la principal limitación de `pipe()`: permiten comunicar procesos no emparentados, ya que el archivo FIFO persiste en el sistema de archivos con una ruta accesible. El comportamiento de bloqueo al abrir el FIFO garantiza sincronización natural entre el proceso que escribe y el que lee, sin necesidad de mecanismos adicionales.

## ¿Cómo podría mejorar esta práctica?

Podría crear dos FIFOs para lograr comunicación bidireccional entre dos procesos completamente independientes (sin relación padre-hijo), lo que simularía mejor un escenario real de IPC entre aplicaciones distintas.

## Salida en pantalla

![fifo](/assets/fifo.png)
*Ejemplo1.*

![fifo2](/assets/fifo2.png)
*Ejemplo1.*