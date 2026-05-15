---
layout: post
title: "5.7 Administracion con Mapas de Bits"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

Con un **mapa de bits**, la memoria se divide en **unidades de asignación** de tamaño fijo. A cada unidad le corresponde un bit en el mapa:
- `0` → unidad libre
- `1` → unidad ocupada

El tamaño de la unidad de asignación es un factor de diseño crítico:

| Unidad pequeña | Unidad grande |
|---|---|
| Mapa de bits más grande | Mapa de bits más pequeño |
| Mayor precisión | Posible desperdicio al final de cada proceso |

Por ejemplo, una memoria de 32n bits necesita n bits de mapa, lo que representa solo 1/33 de la memoria total — una sobrecarga muy pequeña.

**Ventaja principal:** el mapa ocupa una cantidad fija y predecible de memoria, independientemente de cuántos procesos haya.

**Desventaja principal:** cuando se necesita cargar un proceso de k unidades, el administrador debe buscar en el mapa una cadena de **k ceros consecutivos**, lo cual es una operación lenta. Por esta razón, los mapas de bits raramente se usan en la práctica para este propósito.

## Salida en pantalla

Representación conceptual de un mapa de bits de memoria:

```
Unidades:  [0][1][2][3][4][5][6][7][8][9][10][11][12]...
Mapa bits:  1   1   0   0   1   1   1   0   0   0    1   0   0  ...
            ──── P1 ───     ──────── P2 ────     ── libre ──
```

Herramientas para visualizar el uso real de memoria:
```bash
$ vmstat -s -S M
$ cat /proc/meminfo
```