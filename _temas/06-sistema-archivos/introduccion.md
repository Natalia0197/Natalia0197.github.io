---
layout: post
title: "6.1 Introduccion"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

El sistema de archivos en UNIX y Linux tiene una estructura jerárquica y consistente que permite crear, eliminar y manejar archivos de forma dinámica. Lo más importante que aprendí es que el **kernel trabaja con el sistema de archivos a nivel lógico**, sin tratar directamente con los discos físicos.

Cada dispositivo es considerado de forma lógica y tiene asociados dos números: el **número mayor** (identifica el tipo de dispositivo) y el **número menor** (identifica la unidad específica). Estos números son usados para indexar una tabla de funciones que maneja el driver del dispositivo, quien traduce las direcciones lógicas del sistema de archivos a direcciones físicas del disco.

Las principales características del sistema de archivos en UNIX/Linux son:
- Estructura jerárquica de archivos
- Consistencia y protección de datos
- Creación y eliminación dinámica de archivos
- Manejo dinámico del almacenamiento

