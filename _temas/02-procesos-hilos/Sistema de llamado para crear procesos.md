---
layout: post
title: "2.2 Sistema de llamado para crear procesos"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Los procesos pueden ser creados por el sistema operativo desde el momento en que este se inicia, o conforme surge la necesidad de realizar distintas tareas internas. El usuario también puede crear procesos de forma directa mediante la ejecución de programas, o de forma indirecta cuando una aplicación genera nuevos procesos durante su ejecución.

En los sistemas GNU/Linux, la creación de procesos se realiza principalmente a través de la llamada al sistema `fork()`. Esta llamada permite que un proceso existente, denominado **proceso padre**, cree un nuevo proceso llamado **proceso hijo**. Tras la ejecución de `fork()`, el sistema operativo crea un nuevo descriptor de proceso y establece una relación de parentesco entre ambos. Su prototipo es:

```c
#include <sys/types.h>
#include <unistd.h>
pid_t fork(void);
```

Desde el punto de vista lógico, el proceso hijo recibe una copia del espacio de direcciones del proceso padre. Sin embargo, en implementaciones modernas de GNU/Linux esta copia se realiza utilizando la técnica de **copy-on-write (COW)**, lo que significa que las páginas de memoria no se duplican físicamente hasta que alguno de los procesos intenta modificarlas, optimizando así el uso de memoria y el rendimiento del sistema.

## Valores de retorno de `fork()`

Una característica fundamental de `fork()` es que ambos procesos continúan su ejecución a partir de la instrucción siguiente a la llamada, pero con diferentes valores de retorno, lo que permite distinguirlos dentro del programa:

- En el **proceso hijo**, el valor devuelto es `0`.
- En el **proceso padre**, el valor devuelto es el PID del proceso hijo (entero mayor que cero).
- En caso de **error**, `fork()` devuelve `-1` y no se crea ningún proceso hijo.

En los sistemas GNU/Linux, el valor máximo permitido para los PID puede consultarse a través del archivo `/proc/sys/kernel/pid_max`. En arquitecturas de 64 bits, este valor suele ser 4,194,303.

## Ejemplo 1: fork() básico

En el siguiente fragmento, tanto el proceso padre como el proceso hijo ejecutan la instrucción `x = 1` después del retorno de `fork()`. Aunque el código es idéntico, cada proceso cuenta con su propio espacio de direcciones, por lo que la variable `x` en el padre es distinta de la variable `x` en el hijo.

```c
#include <sys/types.h>
#include <stdlib.h>
#include <unistd.h>

int main(void) {
    int x = 0;
    fork();
    x = 1;
    return EXIT_SUCCESS;
}
```

## Atributos heredados por el proceso hijo

El proceso hijo hereda la mayoría de los atributos del proceso padre, entre ellos:

- El entorno de ejecución.
- Los privilegios y credenciales.
- Los descriptores de archivos y dispositivos abiertos.
- La prioridad y los atributos de planificación.

Sin embargo, no todos los atributos son heredados. En particular:

- El hijo recibe un PID distinto.
- Los tiempos de uso de CPU del hijo se inicializan en cero.
- El hijo no hereda los bloqueos mantenidos por el padre.
- Las alarmas establecidas por el padre no generan notificaciones en el hijo.
- El hijo comienza sin señales pendientes, aun cuando el padre las tenga.

## Ejemplo 2: copy-on-write con fork()

En este fragmento, el valor de retorno de `fork()` se utiliza para distinguir entre padre e hijo. Cada uno modifica la variable `x` de manera distinta, lo que provoca que el sistema operativo realice la copia física de la página de memoria que contiene dicha variable.

```c
#include <sys/types.h>
#include <stdio.h>
#include <unistd.h>

int main(void) {
    int x = 0;
    pid_t pid;
    pid = fork();

    if (pid == 0) {
        /* Código ejecutado por el proceso hijo */
        x = 5;
        printf("Hijo: PID=%ld, x=%d\n", (long)getpid(), x);
    } else {
        /* Código ejecutado por el proceso padre */
        x = 10;
        printf("Padre: PID=%ld, x=%d\n", (long)getpid(), x);
    }
}
```

## Ejemplo 3: Arbol binario de procesos
```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <unistd.h>
#include <sys/wait.h>

int main(int argc, char *argv[])
{
    //printf("El numero recibdo es: %d\n", atoi(argv[1]));
    pid_t id_proceso_izq,id_proceso_der;
    int i;
    for (i = 0; i <= atoi(argv[1]); i++)
    {
        fprintf(stdout, "Nivel %d PPID:%ld, PID:%ld\n",i, (long)getppid(),(long)getpid());

        id_proceso_izq = fork();

        if (id_proceso_izq > 0)
        {
            id_proceso_der = fork();

            if(id_proceso_der > 0){
                //fprintf(stdout, "Nivel %d PPID:%ld, PID:%ld, Hijo_izq PID:%ld, Hijo_der PID:%ld\n",i, (long)getppid(),(long)getpid(), (long)id_proceso_izq,(long)id_proceso_der);
                break;
            }
        }
    }
    if (i < atoi(argv[1])){
        wait(NULL);
        wait(NULL);
    }
    return EXIT_SUCCESS;
}
```

Este ejemplo ilustra cómo `fork()` es eficiente en términos de memoria: no se realizan copias innecesarias hasta que realmente se produce una escritura. El mecanismo copy-on-write permite que la creación de procesos sea rápida y escalable, incluso cuando el espacio de direcciones del proceso padre es grande.

## ¿Qué aprendí?

Aprendí que `fork()` es el mecanismo central para la creación de procesos en GNU/Linux. Lo más importante es entender el doble valor de retorno: usando una condición `if (pid == 0)` el hijo ejecuta su propio código, y el padre ejecuta el suyo, aunque físicamente compartan el mismo archivo fuente. También comprendí que copy-on-write hace que la creación de procesos sea eficiente, ya que la memoria no se duplica hasta que es estrictamente necesario.

## ¿Cómo podría mejorar esta práctica?

Podría combinar `fork()` con `wait()` para que el padre espere la terminación del hijo antes de continuar, y usar `WEXITSTATUS` para recuperar el valor de retorno del hijo. Esto haría el ejemplo más completo y cercano a un caso de uso real.

## Salida en pantalla
![fork](/assets/fork1.png)
*Ejemplo 2*

![arbol](/assets/arbol.png)
*Ejemplo 3*