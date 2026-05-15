---
layout: tema
title: "2. Procesos e Hilos"
descripcion: "Gestión de procesos e hilos en Linux"
orden: 2
subtemas:
  - numero: "2.1"
    titulo: "Introducción a Procesos"
    url: "/temas/02-procesos-hilos/introduccion-procesos"
    descripcion: "Concepto de proceso, estados y bloque de control de procesos (PCB)."
  - numero: "2.2"
    titulo: "Sistema de llamado para crear procesos"
    url: "/temas/02-procesos-hilos/Sistema de llamado para crear procesos"
    descripcion: "Uso de la llamada al sistema fork() para crear procesos hijo en Linux."
  - numero: "2.4"
    titulo: "Sistema de llamado para identificar procesos"
    url: "/temas/02-procesos-hilos/Sistema de llamado para identificar procesos"
    descripcion: "Llamadas al sistema para obtener el identificador del proceso actual y del padre."
  - numero: "2.5"
    titulo: "Sistema de llamada wait()"
    url: "/temas/02-procesos-hilos/Sistema de llamada wait()"
    descripcion: "Uso de wait() para sincronizar la ejecución entre procesos padre e hijo."
  - numero: "2.5.1"
    titulo: "Uso de waitpid()"
    url: "/temas/02-procesos-hilos/waitpid"
    descripcion: "Control más preciso de la espera entre procesos usando waitpid()."
  - numero: "2.6"
    titulo: "Sistema de llamada _exit () y exit ()"
    url: "/temas/02-procesos-hilos/exit"
    descripcion: "Diferencias y uso de _exit() y exit() para terminar procesos en Linux."
  - numero: "2.7"
    titulo: "Estado Zombi"
    url: "/temas/02-procesos-hilos/estado-zombi"
    descripcion: "Qué es un proceso zombi, cómo se genera y cómo prevenirlo."
  - numero: "2.8"
    titulo: "Hilos"
    url: "/temas/02-procesos-hilos/hilos"
    descripcion: "Reemplazo de la imagen de un proceso usando la familia de llamadas exec()."
  - numero: "2.8.2"
    titulo: "Creación de Hilos"
    url: "/temas/02-procesos-hilos/creacion-hilos"
    descripcion: "Creación de hilos en C usando pthread_create() y la biblioteca POSIX."
---

## Sobre este tema

Estudio de los procesos e hilos en Linux: su creación, identificación,
sincronización y terminación mediante llamadas al sistema y la biblioteca POSIX pthread.
