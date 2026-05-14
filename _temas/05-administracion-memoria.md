---
layout: tema
title: "5. Administración de Memoria"
descripcion: "Gestión y organización de la memoria en sistemas operativos"
orden: 5
subtemas:
  - numero: "5.1"
    titulo: "Introducción"
    url: "/temas/05-administracion-memoria/introduccion"
    descripcion: "Conceptos fundamentales de la administración de memoria en sistemas operativos."
  - numero: "5.2"
    titulo: "Administración sin Intercambio o Paginación"
    url: "/temas/05-administracion-memoria/sin-intercambio"
    descripcion: "Modelos básicos de administración de memoria sin técnicas de intercambio."
  - numero: "5.3"
    titulo: "Modelos de Multiprogramación"
    url: "/temas/05-administracion-memoria/multiprogramacion"
    descripcion: "Modelos que permiten la ejecución simultánea de múltiples procesos en memoria."
  - numero: "5.4"
    titulo: "Multiprogramación con Particiones Fijas"
    url: "/temas/05-administracion-memoria/particiones-fijas"
    descripcion: "División de la memoria en particiones fijas para alojar múltiples procesos."
  - numero: "5.5"
    titulo: "Reasignación y Protección"
    url: "/temas/05-administracion-memoria/reasignacion-proteccion"
    descripcion: "Mecanismos de reasignación de direcciones y protección entre procesos."
  - numero: "5.6"
    titulo: "Intercambio"
    url: "/temas/05-administracion-memoria/intercambio"
    descripcion: "Técnica de intercambio (swapping) para mover procesos entre memoria y disco."
  - numero: "5.7"
    titulo: "Administración con Mapas de Bits"
    url: "/temas/05-administracion-memoria/mapas-bits"
    descripcion: "Uso de mapas de bits para registrar el estado de los bloques de memoria."
  - numero: "5.8"
    titulo: "Administración con Listas Ligadas"
    url: "/temas/05-administracion-memoria/listas-ligadas"
    descripcion: "Gestión de memoria libre y ocupada mediante estructuras de listas ligadas."
  - numero: "5.9"
    titulo: "Memoria Virtual"
    url: "/temas/05-administracion-memoria/memoria-virtual"
    descripcion: "Concepto de memoria virtual y su implementación mediante paginación."
  - numero: "5.10.1"
    titulo: "Función sysinfo"
    url: "/temas/05-administracion-memoria/sysinfo"
    descripcion: "Uso de sysinfo() para obtener información del estado de la memoria del sistema."
  - numero: "5.10.2"
    titulo: "Funciones mmap y munmap"
    url: "/temas/05-administracion-memoria/mmap-munmap"
    descripcion: "Mapeo de archivos y dispositivos en memoria usando mmap() y munmap()."
---

## Sobre este tema

Estudio de los mecanismos que usa el sistema operativo para gestionar
la memoria: particiones, intercambio, memoria virtual y funciones del sistema
para consultar y mapear memoria en Linux.
