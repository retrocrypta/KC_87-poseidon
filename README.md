KC87 portado de MiST FPGA a Poseidon

Arranca y genera video pero es experimental y para aprender.

# kc87fpga

Esta es una recreación de un KC87 usando VHDL. Está diseñado para ejecutarse en una placa FPGA Terasic DE1. El ordenador
es miembro de una línea de ordenadores domésticos de la antigua Alemania del Este que era bastante popular.
Todos estaban basados en el Z80 y sus periféricos.

Ver http://en.wikipedia.org/wiki/Robotron_KC_87

La mayoría de los programas para este ordenador y toda su documentación está en alemán. Por eso tengo miedo de que así sea.

## Descripción
### UPC
- T80 con extensión para ampliar RETI
- Reloj 50MHz o 2.4576MHz (conmutable)

### RAM
- 32 kB + 32 kB para las roms

### ROM
- ROM de arranque de 8kB/16kB con gestor de arranque empaquetado

### CTC+PIO
- PIO y CTC con capacidad de interrupción
- controlador de interrupción simple con priorización (variante mejorada ToDo)

### Video
- Modo texto 40x24 (falta el modo 40x20)
- Salida 640x768@60Hz (la temporización corresponde a 1024x768@60Hz)
- Las líneas de exploración se pueden activar

### Teclado
- Se simula la matriz del teclado.
- Control mediante PS/2 pasado a USB de MiST 

### Tarjeta SD
- El sistema operativo y el básico se leen y se inician mediante la ROM de arranque.
- los archivos tap se pueden cargar desde la tarjeta

## Servicio
Después de iniciar, primero se descomprime el gestor de arranque y se inicia después de $8000. Luego busca en la tarjeta SD.
según el SO y la Rom Básica. Si los encuentra, se cargan y se inicia el sistema operativo. El gestor de arranque está buscando
a os____f0.87b y basic_c0.87b en el directorio ROMS.

La Rom también está disponible durante el funcionamiento normal. Después de entrar

```
SD
```

Se inicia un pequeño menú en el indicador del sistema operativo. Allí encontrarás algunas instrucciones de funcionamiento.

## Interruptores e indicadores

Tecla 0: Reiniciar
Teclas 1-3: Mostrar vectores de interrupción
SW0: Turbo permanentemente encendido
SW1: Cambiar líneas de exploración
F1: Pausa/Continuación
F2: Astucia
F3: ejecutar
F4: Detener
F5: Color
F6: Gráficos
Display de 7 segmentos: Objetivo de la última interrupción

## Síntesis/compilación de las roms
Desafortunadamente, las versiones más nuevas de Quartuns ya no son compatibles con Cyclone II. La última versión con esto.
Por lo tanto, el diseño se puede traducir a 13.0sp1. Para crear los archivos, el autor simplemente cargó el proyecto y puedes empezar.

La ROM de arranque ya está traducida. Por tanto, no debería ser necesaria una nueva traducción. aqui uno
Descripción por si alguien quiere probarlo...

El software para las roms se escribió principalmente usando SDCC. Además de este compilador, se proporciona lo siguiente
necesario:
- hacer
-objcopia de Binutils (ihex -> bin)
- ZX7 (empaquetador)
- Z80asm (ensamblador Z80)
- TCL (contenedor de conversión -> VHDL)

PRIMERO

```
hacer sdrom
```

Luego se crea la ROM en la carpeta sw.

Para fines de desarrollo o para placas sin ranura para tarjeta SD, existe un monitor que permite la carga a través del puerto serie.
Interfaz dominada.

Variantes útiles:
- bootloader.vhdl (gestor de arranque con OS, Basic y Monitor-ROM)
- bootloader_mon_0000.vhd (monitor a $0000 para probar roms a $8000)
- bootloader_sdcard.vhd (gestor de arranque para tarjeta SD)

## Tareas pendientes
- agregue el modo de video que falta (40x20).
- el acceso a la memoria no tiene estados de espera, por lo que probablemente sea demasiado rápido
- Controlador de interrupciones mejorado que puede interrumpir las interrupciones en curso
- ¿Expansión de gráficos?
- Salida de sonido (¿ruido?).
