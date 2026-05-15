---
layout: post
title: "5.9 Memoria Virtual"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

La **memoria virtual** es la técnica que permite que el tamaño combinado de un programa (código + datos + pila) supere la cantidad de RAM disponible. El sistema operativo mantiene en RAM solo las partes que se necesitan en cada momento; el resto vive en disco (en la partición o archivo de **swap**).

### Paginación

El espacio de direcciones virtuales se divide en unidades llamadas **páginas**, y la RAM física se divide en **marcos de página** del mismo tamaño. La **MMU** (*Memory Management Unit*) traduce direcciones virtuales a físicas en tiempo real.

Ejemplo: si la página virtual 0 (0–4095) está mapeada al marco 2 (8192–12287), la instrucción `MOV REG, 0` hace que la MMU genere el acceso físico a la dirección 8192, de forma completamente transparente para el proceso.

### Swap en Linux

En Ubuntu antes de la versión 17.04 se usaba una **partición swap** separada. Desde 17.04 se usa un **archivo de paginación** (`/swapfile`) en el directorio raíz, sin diferencia de rendimiento.

El parámetro `swappiness` controla cuándo el kernel empieza a usar el swap (valor entre 0–100, predeterminado: 60):

```bash
# Consultar
cat /proc/sys/vm/swappiness

# Cambiar temporalmente (como root)
sysctl -w vm.swappiness=40

# Cambiar permanentemente — editar /etc/sysctl.conf:
vm.swappiness=40
```

Con `swappiness=60`, el kernel usará swap cuando la RAM alcance el 40% de su capacidad.

## ¿Cómo podría mejorar esta práctica?

Observar el archivo `/proc/[pid]/maps` de un proceso en ejecución para ver cómo están mapeadas sus páginas virtuales. Comparar con la salida de `pmap -x [PID]` para entender la distribución real de su espacio de direcciones.

## Salida en pantalla

```bash
$ cat /proc/swaps
Filename        Type        Size    Used    Priority
/swapfile       file        2097148 0       -2

$ cat /proc/$$/maps | head -5
55a1c3400000-55a1c3401000 r--p  /usr/bin/bash
55a1c3401000-55a1c34cb000 r-xp  /usr/bin/bash
55a1c34cb000-55a1c34fc000 r--p  /usr/bin/bash
...
```