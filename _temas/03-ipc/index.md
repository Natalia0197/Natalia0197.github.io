---
layout: tema
title: "3. Mecanismos de Comunicación entre Procesos — IPC"
descripcion: "Comunicación e intercambio de datos entre procesos en Linux"
orden: 3
subtemas:
  - numero: "3.1"
    titulo: "Comunicación mediante tuberías"
    url: "/temas/03-ipc/tuberias"
    descripcion: "Comunicación entre procesos relacionados usando tuberías anónimas con pipe()."
  - numero: "3.1.1"
    titulo: "Tuberías sin Nombre — pipe()"
    url: "/temas/03-ipc/tuberias-pipe"
    descripcion: "Comunicación entre procesos relacionados usando tuberías anónimas con pipe()."
  - numero: "3.1.2"
    titulo: "Tuberías con Nombre — FIFO"
    url: "/temas/03-ipc/tuberias-fifo"
    descripcion: "Comunicación entre procesos no relacionados usando tuberías con nombre FIFO."
  - numero: "3.2"
    titulo: "Mecanismos IPC derivados de System V"
    url: "/temas/03-ipc/mecanismos"
    descripcion: "Generación de llaves únicas para mecanismos IPC de System V usando ftok()."
  - numero: "3.2.1"
    titulo: "Llaves IPC con ftok()"
    url: "/temas/03-ipc/llaves-ipc"
    descripcion: "Generación de llaves únicas para mecanismos IPC de System V usando ftok()."
  - numero: "3.2.2"
    titulo: "Semáforos en System V"
    url: "/temas/03-ipc/semaforos-systemv"
    descripcion: "Sincronización entre procesos usando semáforos derivados de System V."
  - numero: "3.3"
    titulo: "Memoria Compartida"
    url: "/temas/03-ipc/memoria-compartida"
    descripcion: "Compartición de segmentos de memoria entre procesos como mecanismo IPC."
  - numero: "3.4"
    titulo: "Cola de Mensajes"
    url: "/temas/03-ipc/cola-mensajes"
    descripcion: "Comunicación entre procesos mediante colas de mensajes en System V."
  - numero: "3.5"
    titulo: "Información de IPC por Comandos del Sistema"
    url: "/temas/03-ipc/comandos-ipc"
    descripcion: "Uso de ipcs e ipcrm para inspeccionar y liberar recursos IPC activos."
---

## Sobre este tema

Estudio de los mecanismos de comunicación entre procesos en Linux: tuberías,
semáforos, memoria compartida y colas de mensajes, tanto en System V como en POSIX.
