---
layout: post
title: "5.2 Administración sin Intercambio o Paginación"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

Los sistemas de administración de la memoria se pueden clasificar en dos grandes grupos:

- Los que **desplazan** los procesos entre la memoria principal y el disco durante la ejecución (intercambio o paginación).
- Los que **no los desplazan**, manteniendo en memoria todo lo que ejecutan.

El esquema más sencillo de administración de memoria es aquel en el que **solo se tiene un proceso en memoria en cada instante**. En este modelo no existe multiprogramación: el sistema operativo ocupa una parte de la memoria (generalmente en la parte baja o alta), y el proceso de usuario ocupa el resto.

Este enfoque, aunque limitado, tiene la ventaja de ser extremadamente simple:

- No hay necesidad de proteger un proceso de otro.
- No se requiere reasignación de direcciones en tiempo de ejecución.
- No existe fragmentación entre procesos.

Su principal desventaja es la subutilización de la CPU, ya que mientras el único proceso en memoria espera una operación de entrada/salida, el procesador permanece inactivo.

## Transición hacia la multiprogramación

Para mejorar el uso de la CPU, los sistemas modernos cargan múltiples procesos en memoria simultáneamente. Esto introduce los problemas de **reasignación** y **protección** que se abordan en los temas siguientes, y también la necesidad de mecanismos como las particiones fijas, los mapas de bits, las listas ligadas y la memoria virtual.

## ¿Qué aprendí?

Aprendí que los primeros sistemas operativos solo ejecutaban un proceso a la vez, lo que hacía que el modelo de administración de memoria fuera trivial pero extremadamente ineficiente. Entender este modelo base ayuda a comprender por qué surgió la multiprogramación y todos los mecanismos de gestión de memoria que la hacen posible.
