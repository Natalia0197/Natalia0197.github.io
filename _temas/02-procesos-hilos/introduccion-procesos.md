---
layout: post
title: "2.1 Introducción a Procesos"
tema: "2. Procesos e Hilos"
tema_url: /temas/02-procesos-hilos/
---

Todos los sistemas de multiprogramación están construidos en torno al concepto
de proceso. De manera simplificada, en un instante determinado un proceso
puede encontrarse ejecutándose en el procesador o fuera de él a la espera de ser
ejecutado. Bajo esta visión básica, un proceso puede estar en uno de dos estados:
Ejecución o No ejecución.
Para poder administrar los procesos, el sistema operativo debe identificar a cada
uno de ellos y mantener información asociada, como su estado actual, su
ubicación en memoria y otros datos de control. Los procesos que no se
encuentran en ejecución deben almacenarse en alguna estructura, generalmente
colas, donde esperan su turno para ser atendidos por el procesador.

## Estados

Si todos los procesos que no están ejecutándose estuvieran siempre listos para
hacerlo, una única cola de espera sería suficiente. Sin embargo, esta situación no
refleja el comportamiento real de un sistema, ya que algunos procesos se
encuentran listos para ejecutarse, mientras que otros están bloqueados esperando
la finalización de una operación de entrada/salida u otro evento. Por esta razón, el
estado de No ejecución se puede dividir en dos estados diferenciados: Listo y
Bloqueado. Al considerar esta división, y sumando los estados de creación y
finalización, se obtiene un modelo de cinco estados, que se muestra en la Figura siguiente. Estos estados son los siguientes:

• Nuevo. Proceso que acaba de ser creado, pero que aún no ha sido
admitido por el sistema en el conjunto de procesos ejecutables.
• Listo. Proceso que se encuentra preparado para ejecutar y que espera
la asignación del procesador.
• Ejecución. Proceso que está siendo ejecutado actualmente por la CPU.
• Bloqueado Proceso que no puede continuar su ejecución hasta que
ocurra un evento específico, como la finalización de una operación de
entrada/salida. Un proceso puede pasar voluntariamente a este estado,
por ejemplo, mediante una llamada a funciones, como por ejemplo
sleep.
• Terminado. Proceso que ha sido retirado del conjunto de procesos
ejecutables, ya sea porque finalizó su ejecución de forma normal o
porque fue abortado.

![Modelo de cinco estados](/assets/procesos-5-estados.png)

El modelo de cinco estados permite al sistema gestionar de manera eficiente la
ejecución concurrente de múltiples procesos y optimizar el uso del procesador. En
los sistemas UNIX, el diagrama de estados de los procesos es más complejo que
el modelo básico anterior, debido a la distinción entre modos de ejecución y a la
existencia de estados adicionales relacionados con la gestión de memoria y la
finalización de los procesos. Un proceso en UNIX puede encontrarse en alguno de
los siguientes estados o situaciones:
• Ejecutándose en modo usuario, cuando el proceso ejecuta código de
aplicación.
• Ejecutándose en modo kernel, cuando el proceso se encuentra atendiendo
una llamada al sistema o una interrupción.
• Listo para ejecutarse, pero no en ejecución, esperando a que el kernel le
asigne el procesador.
• Dormido en memoria, cuando el proceso se encuentra bloqueado y
cargado en memoria, a la espera de que ocurra un evento.
• Listo para ejecutarse, pero suspendido, esperando a que el swapper
(proceso 0) lo cargue en memoria principal.
• En transición, estado en el que el proceso ha sido creado, pero aún no está
completamente preparado para ejecutar.
• Finalizando, cuando el proceso ejecuta la llamada al sistema exit.
• Zombi, estado en el que el proceso ya ha terminado su ejecución y no
existe como entidad ejecutable, pero conserva una entrada en la tabla de
procesos para que su proceso padre pueda recuperar el código de salida y
cierta información estadística, como los tiempos de ejecución.
El estado zombi representa el estado final de un proceso en UNIX y desaparece
únicamente cuando el proceso padre recoge su estado de terminación.