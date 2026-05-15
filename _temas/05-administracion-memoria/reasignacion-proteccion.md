---
layout: post
title: "5.5 Reasignacion y Proteccion"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

La multiprogramación introduce dos problemas esenciales:

### Reasignación
Los procesos se ejecutan en direcciones distintas de memoria. Si un programa contiene una instrucción `CALL 100` y se carga en la partición 1 (que empieza en la dirección 100k), esa instrucción debe interpretarse como `CALL 100k + 100`. Si en cambio se carga en la partición 2 (200k), debe ser `CALL 200k + 100`. La instrucción en el binario no cambia, pero la dirección real debe ajustarse en tiempo de ejecución.

### Protección
Sin protección, un proceso podría leer o escribir en la memoria de otro proceso (o del propio sistema operativo). Esto es inaceptable en un sistema multiprogramado.

### Solución: registros base y límite
La solución clásica usa **dos registros de hardware especiales**:

- **Registro base**: contiene la dirección de inicio de la partición del proceso actual.
- **Registro límite**: contiene la longitud de esa partición.

Cuando el proceso genera una dirección de memoria, el hardware le suma automáticamente el valor del registro base antes de acceder a RAM. Además, verifica que la dirección resultante no supere el límite, evitando que el proceso acceda a memoria fuera de su partición.

Por ejemplo, si el registro base vale 100k y el proceso genera la dirección 100, el hardware accede en realidad a 100k + 100, todo de forma transparente para el proceso.

## Salida en pantalla

Intentar acceder a memoria fuera del espacio del proceso genera una señal SIGSEGV:

```
Segmentation fault (core dumped)
```

Esto es justamente el mecanismo de protección del kernel en acción.