---
layout: post
title: "6.4.1 Funcion ioctl"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

La función `ioctl()` permite realizar operaciones de control sobre dispositivos de carácter. Es una interfaz genérica para enviar comandos específicos del dispositivo desde espacio de usuario al kernel.

**Prototipo:**
```c
#include <sys/ioctl.h>
int ioctl(int fd, unsigned long request, char *argp, ...);
```

- `fd`: descriptor del archivo de dispositivo abierto
- `request`: código de solicitud dependiente del dispositivo
- `argp`: apuntador a los parámetros de la solicitud

Retorna `0` en caso de éxito, `-1` en caso de error.

**Ejemplo 1 — obtener el tamaño de la terminal:**
```c
#include <sys/ioctl.h>
struct winsize w;
ioctl(STDOUT_FILENO, TIOCGWINSZ, &w);
printf("Filas: %d\n", w.ws_row);
printf("Columnas: %d\n", w.ws_col);
```
El macro `TIOCGWINSZ` (Get Window Size) consulta las dimensiones actuales de la ventana de la terminal.

**Ejemplo 2 — obtener IP y MAC de una interfaz de red:**
```c
#include <sys/ioctl.h>
#include <net/if.h>
struct ifreq ifr;
int sock = socket(AF_INET, SOCK_DGRAM, 0);
strncpy(ifr.ifr_name, "eth0", IFNAMSIZ - 1);
ioctl(sock, SIOCGIFADDR, &ifr);   // obtiene dirección IP
ioctl(sock, SIOCGIFHWADDR, &ifr); // obtiene dirección MAC
```

## ¿Cómo podría mejorar esta práctica?

Explorar otros macros de `ioctl` como `TIOCSWINSZ` (cambiar tamaño de ventana) o `FIONREAD` (consultar bytes disponibles en un descriptor). También probar la consulta de diferentes interfaces de red en el sistema.

## Salida en pantalla

```
Filas: 24
Columnas: 80

IP de wlp8s0: 192.168.1.105
MAC de wlp8s0: a4:5e:60:d2:11:3f
```