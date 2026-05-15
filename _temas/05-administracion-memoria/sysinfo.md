---
layout: post
title: "5.10.1 Funcion sysinfo"
tema: "5. Administración de Memoria"
tema_url: /temas/05-administracion-memoria/
---

## ¿Qué aprendí?

La función `sysinfo()` permite obtener estadísticas generales del sistema, incluyendo el estado de la memoria RAM y el swap:

```c
#include <sys/sysinfo.h>
int sysinfo(struct sysinfo *info);
```

Retorna `0` en éxito. La estructura `sysinfo` contiene:

```c
struct sysinfo {
    long uptime;              // Segundos desde el boot
    unsigned long loads[3];   // Carga promedio: 1, 5 y 15 minutos
    unsigned long totalram;   // Memoria RAM total
    unsigned long freeram;    // Memoria RAM libre
    unsigned long sharedram;  // Memoria compartida
    unsigned long bufferram;  // Memoria usada por buffers
    unsigned long totalswap;  // Swap total
    unsigned long freeswap;   // Swap libre
    unsigned short procs;     // Número de procesos activos
    unsigned long totalhigh;  // Memoria alta total
    unsigned long freehigh;   // Memoria alta libre
    unsigned int mem_unit;    // Tamaño de unidad de memoria en bytes
};
```

Los datos provienen de `/proc/meminfo` y `/proc/loadavg`. El archivo `loadavg` muestra: promedio de carga (1 min, 5 min, 15 min) / procesos en ejecución sobre total / PID del proceso más reciente.

**Ejemplo de programa:**

```c
#include <stdio.h>
#include <stdlib.h>
#include <sys/sysinfo.h>

#define minuto 60
#define hora   (minuto * 60)
#define dia    (hora * 24)
#define KB     1024

int main() {
    struct sysinfo si;
    sysinfo(&si);
    printf("Tiempo del sistema: %ld dias, %ld:%02ld:%02ld\n",
           si.uptime/dia, (si.uptime%dia)/hora,
           (si.uptime%hora)/minuto, si.uptime%minuto);
    printf("Total RAM: %ld KB\n", si.totalram / KB);
    printf("Libre RAM: %ld KB\n", si.freeram  / KB);
    printf("Swap:      %ld KB\n", si.totalswap / KB);
    printf("Procesos:  %d\n",     si.procs);
    return EXIT_SUCCESS;
}
```

## ¿Cómo podría mejorar esta práctica?

Extender el programa para mostrar el porcentaje de RAM y swap usados, y comparar la salida con el comando `free -h` para verificar que los valores coinciden.

## Salida en pantalla

```
Tiempo del sistema: 2 dias, 14:32:07
Total RAM: 8015136 KB
Libre RAM:  428528 KB
Swap:       999420 KB
Procesos:      312
```