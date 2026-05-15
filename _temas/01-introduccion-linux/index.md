---
layout: tema
title: "1. Introducción al Sistema Operativo Linux"
descripcion: "Fundamentos del sistema operativo Linux"
orden: 1

---

## Sobre este tema

Introducción general al sistema operativo Linux: su historia, estructura,
comandos básicos y clasificación dentro de los sistemas operativos modernos.




-------------------------------------------




Linux es un sistema operativo de código abierto basado en Unix, creado en 1991 por el estudiante finlandés Linus Torvalds. Lo que comenzó como un proyecto personal se convirtió en uno de los sistemas operativos más importantes e influyentes del mundo.

Un sistema operativo es el software fundamental que actúa como intermediario entre el hardware y el usuario. Sin él, ningún programa podría ejecutarse. Linux se destaca por ser de código abierto, lo que significa que su código fuente es público y cualquiera puede estudiarlo, modificarlo y distribuirlo libremente. Además, es multiusuario y multitarea, permitiendo que varios usuarios trabajen simultáneamente ejecutando múltiples procesos. Es reconocido por su seguridad, estabilidad y portabilidad, funcionando en una enorme variedad de hardware, desde supercomputadoras hasta teléfonos Android, y en la mayoría de sus distribuciones es completamente gratuito.

Su estructura se compone de tres capas fundamentales: el Kernel, que es el núcleo del sistema y gestiona el hardware, la memoria y los procesos; el Shell, que es la interfaz entre el usuario y el kernel; y las Aplicaciones, que son los programas que el usuario utiliza directamente.

Linux no viene en una sola versión, sino en múltiples distribuciones o "distros" adaptadas a diferentes necesidades. Entre las más populares se encuentran Ubuntu, ideal para principiantes; Debian, orientado a servidores; Fedora, preferido por desarrolladores; Arch Linux, para usuarios avanzados; y Kali Linux, especializado en ciberseguridad.

Hoy en día, Linux está en todas partes: el 90% de los servidores web del mundo lo utilizan, es la base del sistema operativo Android, impulsa supercomputadoras, dispositivos IoT y sistemas embebidos. Sin duda, Linux es la columna vertebral de la infraestructura tecnológica moderna.

---

## Comparación con otros sistemas operativos

Para entender mejor qué hace especial a Linux, es útil compararlo con los sistemas operativos más utilizados en el mundo: Windows y macOS.

| Característica | Linux | Windows | macOS |
|---|---|---|---|
| Código fuente | Abierto (GPL) | Cerrado | Parcialmente abierto |
| Costo | Gratuito | De pago | Gratuito (requiere hardware Apple) |
| Seguridad | Alta | Media | Alta |
| Personalización | Total | Limitada | Limitada |
| Uso principal | Servidores, desarrollo | Uso doméstico y empresarial | Diseño, uso doméstico |
| Interfaz | Terminal y gráfica | Gráfica | Gráfica |

Windows es el sistema más utilizado en computadoras personales por su facilidad de uso, pero es de código cerrado y requiere licencia de pago. macOS, desarrollado por Apple, ofrece una experiencia muy pulida pero está limitado al hardware de la misma empresa. Linux, en cambio, es completamente libre, altamente personalizable y domina en entornos de servidores, desarrollo de software y cómputo científico.

---

## Comandos básicos en Linux

La interacción con Linux se realiza principalmente a través de la terminal, también llamada línea de comandos. A continuación se presentan los comandos esenciales para comenzar.

### Navegación del sistema de archivos

```bash
pwd        # Muestra el directorio actual en el que te encuentras
ls         # Lista los archivos y carpetas del directorio actual
ls -la     # Lista todos los archivos, incluyendo ocultos, con detalles
cd /ruta   # Cambia al directorio especificado
cd ..      # Sube un nivel en la jerarquía de directorios
cd ~       # Regresa al directorio personal del usuario
```

### Gestión de archivos y directorios

```bash
mkdir nombre       # Crea un nuevo directorio
touch archivo.txt  # Crea un archivo vacío
cp origen destino  # Copia un archivo o directorio
mv origen destino  # Mueve o renombra un archivo
rm archivo.txt     # Elimina un archivo
rm -r carpeta      # Elimina una carpeta y su contenido
```

### Información del sistema

```bash
whoami     # Muestra el nombre del usuario actual
uname -a   # Muestra información completa del sistema
man ls     # Abre el manual del comando especificado (en este caso, ls)
clear      # Limpia la pantalla de la terminal
```

### Práctica guiada

Sigue estos pasos en la terminal para poner en práctica los comandos anteriores:

```bash
# 1. Verifica en qué directorio estás
pwd

# 2. Crea una carpeta llamada "practica"
mkdir practica

# 3. Entra a esa carpeta
cd practica

# 4. Crea un archivo de texto
touch mi_primer_archivo.txt

# 5. Verifica que se creó
ls

# 6. Regresa al directorio anterior
cd ..
```