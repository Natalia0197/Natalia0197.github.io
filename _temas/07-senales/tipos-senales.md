---
layout: post
title: "7.2 Tipos de Senales"
tema: "7. Señales"
tema_url: /temas/07-senales/
---

## ¿Qué aprendí?

Cada señal tiene un número entero positivo y un nombre (definido en `<signal.h>`). Las señales se clasifican en los siguientes grupos:

- **Relacionadas con terminación de procesos**: notifican el fin de un proceso hijo o solicitan terminar.
- **Excepciones de proceso**: acceso fuera del espacio de direcciones, errores en coma flotante, etc.
- **Errores irrecuperables de llamadas al sistema**: ocurren dentro de llamadas al sistema fallidas.
- **Originadas en modo usuario**: enviadas explícitamente entre procesos vía `kill()`, o por temporizadores.
- **Interacción con la terminal**: generadas al presionar combinaciones de teclas como Ctrl+C o Ctrl+Z.
- **Ejecución paso a paso**: usadas por depuradores.

Las principales señales definidas en UNIX System V son:

| Nombre | Núm | Descripción | Acción por defecto |
|--------|-----|-------------|-------------------|
| SIGHUP | 1 | Terminal desconectada o líder del grupo terminó | Terminar |
| SIGINT | 2 | Tecla de interrupción (Ctrl+C) | Terminar |
| SIGQUIT | 3 | Tecla de salida (Ctrl+\\) | Core + Terminar |
| SIGILL | 4 | Instrucción ilegal detectada por hardware | Core + Terminar |
| SIGFPE | 8 | Error en operación de coma flotante | Core + Terminar |
| SIGKILL | 9 | Terminación irremediable del proceso | Terminar (no ignorable) |
| SIGSEGV | 11 | Violación de segmento de memoria | Core + Terminar |
| SIGPIPE | 13 | Escritura en tubería sin lectores | Terminar |
| SIGALRM | 14 | Temporizador expirado (alarm) | Terminar |
| SIGTERM | 15 | Finalización de software (puede ignorarse) | Terminar |
| SIGUSR1 | 16 | Señal reservada para el programador | Terminar |
| SIGUSR2 | 17 | Señal reservada para el programador | Terminar |
| SIGCLD | 18 | Muerte de proceso hijo | Ignorar |
