---
layout: post
title: "3.5 Información de IPC por Comandos del Sistema"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

En GNU/Linux se puede obtener información sobre los objetos IPC activos por medio del comando `ipcs`. Por defecto muestra información sobre los tres tipos de mecanismos IPC: segmentos de memoria compartida, colas de mensajes y arreglos de semáforos.

## Comando `ipcs`

```bash
$ ipcs
```

Ejemplo de salida:

```
------ Colas de mensajes -----
key        msqid      propietario perms      bytes utilizados mensajes

---- Segmentos memoria compartida ----
key        shmid      propietario perms      bytes      nattch     estado
0x00000000 884743     usuario     600        1048576    2          dest
0x00000000 819208     usuario     600        524288     2          dest

------ Matrices semáforo -------
key        semid      propietario perms      nsems
0x6100050e 1          usuario     600        2
0x61000c49 2          usuario     600        2
```

## Opciones útiles de `ipcs`

| Opción | Descripción |
|---|---|
| `ipcs -m` | Muestra solo segmentos de memoria compartida |
| `ipcs -q` | Muestra solo colas de mensajes |
| `ipcs -s` | Muestra solo semáforos |
| `ipcs -a` | Muestra toda la información disponible |

## Comando `ipcrm` — Eliminar recursos IPC

Para eliminar un recurso IPC manualmente:

```bash
ipcrm -m <shmid>   # Eliminar segmento de memoria compartida
ipcrm -q <msqid>   # Eliminar cola de mensajes
ipcrm -s <semid>   # Eliminar semáforo
```

## Archivos en `/proc/sysvipc`

Los objetos IPC también se pueden localizar en el directorio `/proc/sysvipc`, donde se ubican tres archivos de lectura:

```bash
$ ls /proc/sysvipc
msg  sem  shm
```

Por ejemplo, para ver los semáforos activos:

```bash
$ cat /proc/sysvipc/sem
key      semid perms  nsems  uid  gid  cuid cgid     otime      ctime
1627391246  1   600    2    1000 1000 1000 1000  ...
```

## Límites del sistema

El sistema tiene un límite asociado a cada mecanismo IPC para prevenir la creación arbitraria de recursos. Dichos límites pueden consultarse y modificarse en `/proc/sys/kernel`:

```bash
cat /proc/sys/kernel/shmmax    # Tamaño máximo de segmento de memoria compartida
cat /proc/sys/kernel/sem       # Límites de semáforos
cat /proc/sys/kernel/msgmax    # Tamaño máximo de mensaje en cola
```

## ¿Qué aprendí?

Aprendí que `ipcs` e `ipcrm` son herramientas indispensables para depurar programas que usan IPC, ya que los recursos de System V **persisten en el sistema** después de que el proceso termina si no son eliminados explícitamente con `IPC_RMID`. Esto puede causar acumulación de recursos huérfanos que agotan los límites del sistema.
