---
layout: post
title: "3.1 Comunicación mediante tuberías"
tema: "3. IPC"
tema_url: /temas/03-ipc/
---

La comunicación entre procesos es fundamental para que intercambien datos. Para llevarla a cabo hay que considerar si los procesos van a comunicarse en la misma máquina y si están emparentados, y si van a comunicarse desde máquinas diferentes.

Las tuberías son mecanismos clásicos de comunicación entre dos o más procesos emparentados en la misma máquina. El sistema ofrece formas básicas de comunicación como streams (pipe, FIFO y sockets) y mensajes (colas de mensajes y sockets datagramas). Si los procesos son parientes, la comunicación puede realizarse por medio de una **tubería o pipe**; si se necesita proteger el medio de comunicación, se pueden utilizar mecanismos de sincronización implementados con llaves (System V) o sin llaves mediante llamadas POSIX.

Las tuberías presentan dos limitaciones fundamentales:

- Los datos fluyen en una sola dirección (half-duplex).
- Solo pueden usarse entre procesos que tienen un ancestro en común.
