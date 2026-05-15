---
layout: post
title: "5.8 Administracion con Listas Ligadas"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

En la administración con **listas ligadas**, cada entrada de la lista describe un segmento de memoria con los campos:
- Tipo: **H** (hueco libre) o **P** (proceso ocupando)
- Dirección de inicio
- Longitud
- Apuntador a la siguiente entrada

La lista se mantiene **ordenada por direcciones**, lo que facilita actualizar la lista al terminar o intercambiar un proceso.

Existen cuatro algoritmos clásicos para asignar memoria a un proceso nuevo:

**Primero en ajustarse** *(First Fit)*
Recorre la lista hasta encontrar el primer hueco suficientemente grande. Es el más rápido y es el que usa UNIX para asignación de memoria.

**Siguiente en ajustarse** *(Next Fit)*
Como First Fit, pero recuerda dónde quedó la última búsqueda y continúa desde ahí. Evita revisar siempre desde el inicio.

**Mejor ajuste** *(Best Fit)*
Busca en toda la lista el hueco más pequeño que sea suficiente. Minimiza el desperdicio por hueco, pero deja fragmentos muy pequeños e inútiles.

**Peor ajuste** *(Worst Fit)*
Asigna siempre el hueco más grande disponible, con la idea de que el remanente sea útil para futuros procesos.

Para mejorar la velocidad, se pueden usar **dos listas separadas**: una para procesos y otra para huecos. Así, los algoritmos solo recorren la lista de huecos. El costo es mayor complejidad al liberar memoria, ya que hay que actualizar ambas listas.

## Salida en pantalla

Representación de la lista ligada de memoria:

```
[P | inicio:0    | long:20k] →
[H | inicio:20k  | long:10k] →
[P | inicio:30k  | long:40k] →
[H | inicio:70k  | long:5k ] →
[P | inicio:75k  | long:25k] → NULL
```

Comandos para monitorear la memoria en tiempo real:
```bash
$ top          # uso por proceso
$ pmap -x [PID]  # mapa de memoria de un proceso específico
```