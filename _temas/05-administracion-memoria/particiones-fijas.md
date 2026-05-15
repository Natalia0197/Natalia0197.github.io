---
layout: post
title: "5.4 Multiprogramacion con Particiones Fijas"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

Para ejecutar múltiples procesos simultáneamente, la forma más sencilla de organizar la memoria es dividirla en **n particiones fijas**, que pueden ser de tamaños distintos.

Existen dos estrategias para organizar las colas de entrada:

**a) Colas independientes por partición**
Cada partición tiene su propia cola. La desventaja es evidente: si la cola de una partición grande está vacía pero la de una pequeña está llena, la memoria grande se desperdicia.

**b) Cola única**
Cuando se libera una partición, se carga el proceso más cercano al frente de la cola que se ajuste a ella. Una variante más eficiente busca en toda la cola el trabajo más grande que quepa, aunque esto discrimina a los procesos pequeños.

Para evitar esa discriminación se pueden aplicar reglas como:
- Reservar siempre una **partición pequeña** disponible para tareas pequeñas.
- Llevar un contador de exclusiones: si un proceso es excluido más de **k veces**, ya no puede ser omitido.

La principal limitación de las particiones fijas es la **fragmentación interna**: si un proceso es más pequeño que su partición, el espacio sobrante se desperdicia.

## ¿Cómo podría mejorar esta práctica?

Simular con un programa en C el comportamiento de ambas estrategias de cola (independiente vs. única) para un conjunto de procesos con distintos tamaños, midiendo el desperdicio de memoria en cada caso.

## Salida en pantalla

Esta práctica es teórica/conceptual. Para visualizar la memoria disponible en el sistema:

```bash
$ free -h
              total   usado  libre  compartido  caché  disponible
Memoria:      7.6G    3.1G   512M       420M    3.9G       3.8G
Swap:         975M     12M   963M
```