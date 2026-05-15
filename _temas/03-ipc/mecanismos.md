---
layout: post
title: "3.2 Mecanismos IPC derivados de System V"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

El paquete de comunicación entre procesos de los sistemas UNIX System V y derivados, como GNU/Linux, se compone de tres mecanismos:

1. **Semáforos**: permiten sincronizar el acceso a recursos compartidos.
2. **Memoria compartida**: permite que los procesos compartan su espacio de direcciones virtuales.
3. **Colas de mensajes**: posibilitan el intercambio de datos con un formato determinado.

Estos tres mecanismos comparten características comunes:

- Una tabla con entradas que describen el uso del mecanismo.
- Una llave numérica elegida por el usuario para cada entrada de la tabla.
- Cada mecanismo dispone de una llamada `get` para crear una entrada nueva o recuperar una ya existente.
- Cada entrada tiene un registro de permisos que incluye el ID del usuario y grupo del proceso que reservó la entrada.
- Una llamada de control (`ctl`) que permite leer, modificar el estado de una entrada, o liberarla.

## Resumen de llamadas IPC

| Mecanismo | Biblioteca | Crear/Abrir | Control | Operaciones |
|---|---|---|---|---|
| Semáforos | `<sys/sem.h>` | `semget` | `semctl` | `semop` |
| Memoria compartida | `<sys/shm.h>` | `shmget` | `shmctl` | `shmat`, `shmdt` |
| Cola de mensajes | `<sys/msg.h>` | `msgget` | `msgctl` | `msgsnd`, `msgrcv` |

Todos requieren además `<sys/types.h>` y `<sys/ipc.h>`.

## Tipos de espacios de nombres en IPC

| Mecanismo | Tipo de nombre | Identificación |
|---|---|---|
| pipe | Sin nombre | Descriptor de archivo |
| FIFO | Nombre de ruta | Descriptor de archivo |
| Cola de mensajes | Llave `key_t` | Identificador |
| Memoria compartida | Llave `key_t` | Identificador |
| Semáforo | Llave `key_t` | Identificador |
