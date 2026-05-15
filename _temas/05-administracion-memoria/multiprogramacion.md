---
layout: post
title: "5.3 Modelos de Multiprogramación"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

El uso de la CPU se puede mejorar sustancialmente mediante la **multiprogramación**, que consiste en mantener varios procesos en memoria al mismo tiempo para que la CPU siempre tenga trabajo disponible cuando un proceso queda en espera de E/S.

## Modelo probabilístico

Un mejor modelo para analizar el aprovechamiento de la CPU es el probabilístico. Supongamos que un proceso ocupa una fracción **p** de su tiempo en estado de espera de E/S. Si **n** procesos se encuentran en memoria al mismo tiempo, la probabilidad de que todos ellos esperen por E/S simultáneamente (dejando la CPU inactiva) es:

```
P(CPU inactiva) = p^n
```

Por lo tanto, el **uso de la CPU** se expresa como:

```
CPU = 1 - p^n
```

Por ejemplo, si cada proceso pasa el 80% de su tiempo esperando E/S (p = 0.8):

| Procesos en memoria (n) | Uso de CPU |
|---|---|
| 1 | 20% |
| 2 | 36% |
| 4 | 59% |
| 8 | 83% |
| 16 | 97% |

Este modelo muestra claramente cómo aumentar el grado de multiprogramación mejora el uso de la CPU, aunque con rendimientos decrecientes a medida que n crece.

## Consideración práctica

Este modelo es una simplificación, ya que asume que los procesos esperan E/S de forma independiente. En la práctica los procesos pueden competir por recursos compartidos (disco, red, semáforos), lo que reduce el beneficio real. Sin embargo, el modelo es útil para dimensionar cuánta memoria se necesita para mantener la CPU ocupada dado un porcentaje típico de espera de E/S de los procesos.

## ¿Qué aprendí?

Aprendí que la multiprogramación no se trata solo de ejecutar varios programas, sino de aprovechar los tiempos muertos de cada proceso para avanzar en otros. El modelo probabilístico me ayudó a entender cuantitativamente por qué más procesos en memoria se traduce en mejor uso del procesador, y por qué los sistemas operativos modernos se esfuerzan en mantener un grado de multiprogramación alto.
