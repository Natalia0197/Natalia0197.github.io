---
layout: tema
title: "7. Señales"
descripcion: "Mecanismo de notificación asíncrona entre procesos en Linux"
orden: 7
subtemas:
  - numero: "7.1"
    titulo: "Introducción"
    url: "/temas/07-senales/introduccion"
    descripcion: "Concepto de señal y su rol como mecanismo de comunicación asíncrona en Linux."
  - numero: "7.2"
    titulo: "Tipos de Señales"
    url: "/temas/07-senales/tipos-senales"
    descripcion: "Clasificación y descripción de los principales tipos de señales en Linux."
  - numero: "7.2.1"
    titulo: "Señales en Linux"
    url: "/temas/07-senales/senales-linux"
    descripcion: "Catálogo de señales definidas en Linux y su comportamiento por defecto."
  - numero: "7.3"
    titulo: "Tratamiento de Señales"
    url: "/temas/07-senales/tratamiento-senales"
    descripcion: "Manejo y captura de señales usando signal() y sigaction() en Linux."
  - numero: "7.3.1"
    titulo: "Funciones setjmp y longjmp"
    url: "/temas/07-senales/setjmp-longjmp"
    descripcion: "Uso de setjmp() y longjmp() para saltos no locales en el manejo de señales."
  - numero: "7.4"
    titulo: "Función Alarma y Pausa"
    url: "/temas/07-senales/alarma-pausa"
    descripcion: "Uso de alarm() y pause() para temporización y espera de señales en Linux."
---

## Sobre este tema

Estudio del mecanismo de señales en Linux: tipos, tratamiento,
captura y uso de funciones como signal(), sigaction(), alarm() y pause()
para la comunicación asíncrona entre procesos.
