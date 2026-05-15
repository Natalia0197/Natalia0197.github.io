---
layout: post
title: "6.4.2 Unidad de Disco"
tema: "6. Sistema de Archivos"
tema_url: /temas/06-sistema-archivos/
---

## ¿Qué aprendí?

Una **unidad de disco** es un medio magnético que almacena datos organizados en:

- **Pistas**: círculos concéntricos en el plato magnético
- **Sectores**: divisiones de las pistas, generalmente de **512 bytes** (la unidad mínima de lectura/escritura)

Los tiempos relevantes en una operación de disco son:
- **Tiempo de búsqueda**: tiempo en posicionar los cabezales en la pista correcta
- **Retardo de giro (latencia rotacional)**: tiempo en alinear el sector correcto con la cabeza
- **Tiempo de acceso**: suma de los dos anteriores

Cada **partición** del disco es tratada por el kernel como un dispositivo separado en `/dev`. Las particiones contienen generalmente un sistema de archivos y un área de swap.

Comandos útiles para explorar el disco:
```bash
# Velocidad de lectura del disco
$ sudo hdparm -t /dev/sda
Timing buffered disk reads: 186 MB in 3.01 seconds = 61.83 MB/sec

# Área de swap
$ swapon
NAME      TYPE      SIZE  USED PRIO
/dev/dm-1 partition 976M 13.4M   -2

# Uso de memoria incluyendo swap
$ free -h
              total   usado  libre  compartido  caché  disponible
Memoria:      7.6G    3.3G   408M       549M    3.9G       3.5G
Swap:         975M     13M   962M
```

Otros comandos útiles: `df` (espacio libre), `du` (uso por directorio), `lsblk` (dispositivos de bloque), `blkid` (UUIDs de particiones), `fsck` (verificar sistema de archivos).

## ¿Cómo podría mejorar esta práctica?

Instalar y explorar las herramientas de monitoreo sugeridas: `iotop` para ver E/S por proceso, `iostat` para estadísticas de dispositivos, y `dstat` para una vista consolidada de recursos del sistema.

## Salida en pantalla

```bash
$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 465.8G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0   488M  0 part /boot
└─sda3   8:3    0 464.8G  0 part
  ├─dm-0       0 463.9G  0 lvm  /
  └─dm-1       0   976M  0 lvm  [SWAP]
```