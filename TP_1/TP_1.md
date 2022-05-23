# **Sistemas Embebidos (86.65) - Trabajo Práctico N°1**
## **Facultad de Ingeniería - U.B.A**

### **Integrantes:**

  *Aranda Cristian - 93631 -  carandac@fi.uba.ar*

  *Hernández Juan Manuel - 102196 - jmhernandez@fi.uba.ar*

  *Meoli Lucas - 102190 - lmeoli@fi.uba.ar*
  
## 1) Guía para compilar y debuggear 

En primer lugar, se debe proceder a instalar el CIAA Launcher, cargar el proyecto firmware_v3 e instalar el plug in de Yakindu como se indica los siguientes links:

- https://github.com/epernia/software.
- https://drive.google.com/drive/folders/1zd6Gdea687fpQ7gfa3lu9GBVB7Hph5qz
- https://github.com/epernia/firmware_v3/blob/master/documentation/firmware/eclipse/usage-es.md

Una vez realizados las indicaciones anteriores, se debe conectar la placa EDU-CIAA y realizar los siguientes pasos:

#### 1.1 - Selección de programa y placa

En el archivo Makefile se configura la placa a utilizar y el programa a compilar: 

- BOARD = edu_ciaa_nxp
- PROGRAM_PATH = \<Direccion del ejemplo a compilar>
- PROGRAM_NAME = \<Nombre del ejemplo a compilar>

![Makefile](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Makefile.png)

#### 1.2 Generación de código Yakindu

Dentro de la carpeta del ejemplo a compilar, deben existir 2 archivos, un .sct y un .sgen. El archivo con extensión .sct es el que contiene la máquina de estado del ejemplo.
El archivo .sgen es el encargado de generar los archivos correspondientes a la máquina de estados. Genera 3 tipos de archivos: un \<nombre>.c, un \<nombre>.h y un \<nombre_requiered>.c.
Los dos primeros archivos, corresponde al código generado por Yakindu, y el último archivo indica cuales son las funciones que debemos generar nosotros. 

Para Generar el código se debe hacer clic derecho sobre \<nombre>.sgen => Generate Code Artifacts.

![Generate code artifacts](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Generate_code_artifacts.png)

#### 1.3 Compilación y debug

Una vez generado el codigo, se debe compilar haciendo clic en el martillo de arriba a la izquierda o haciendo clic derecho sobre el proyecto => Build Project.

Luego, para debagguear se debe hacer clic en la flecha que se encuentra al costado del botón de debug y seleccionar Debug Configurations.

![Debug_1](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_1.png)

Al abrirse la nueva pestaña, se debe asegurar que esten configuradas las siguientes opciones:

- Se encuentre seleccionado GDB OpenOCD Debbugging.
- El proyecto sea firmware_v3
- C/C++ Application sea el ejemplo que se desea debaggear.

![Debug_2](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_2.png)

En caso de querer modificar el proyecto se debe hacer clic en Search Project y seleccionar el indicado.

![Debug_3](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_3.png)

#### 2.1 Explicación de los ejemplos de statechart de la sapi

A continuacion se muestra una explicación de los ejemplos de diagrama de estado que contiene la biblioteca *sapi*. En todos los ejemplos se ha simulado su comportamiento como lo indica la siguiente figura

![simulationStateChart](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/simulationStateChart.png)


El primer ejemplo *toggle.sct* es un diagrama de estado implementado con yakindu, en este ejemplo se ha definido un solo evento que enciende y apaga el LED con un tiempo determinado por un delay. En particular este tiempo controla todo el ciclo del programa *main.c*. Desde el lado del desarrollador yakindu solicita que en el archivo *ToggleRequired.h*, que se implementan determinadas funciones que trabajan como la interfaz entre nuestro diagrama de estados y la placa electrónica de desarrollo. Por otro lado, esta función definida en nuestro *main.c*, será utilizada por yakindu para implementar las acciones necesarias en base a los eventos. Para este caso se ha implementado la función *extern void toggleIface_opLED(const Toggle* handle, const sc_integer LEDNumber);* , que enciende y apaga el LED.

![Toggle](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/toggle.PNG)

El segundo ejemplo blink.sct también es un diagrama de estado simple, que implementa sus eventos como timers para encender y apagar el LED 3. Se modificó la función *opLED*  de forma tal que reciba una constante boolena de estado ON, OFF. Para el manejo de los timer se utilizó los archivos *TimerTicks.h* y *TimerTicks.c* de la biblioteca sapi para implementar las siguientes funciones que responden a los eventos *after x ms*. 

- *extern void blink_setTimer(Blink* handle, const sc_eventid evid, const sc_integer time_ms, const sc_boolean periodic);*
- *extern void blink_unsetTimer(Blink* handle, const sc_eventid evid);*
- 
Estas funciones trabajan con interrupciones y son utilizadas por las acciones definidas en el código generado por yakindu.

![Toggle](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/blink.PNG)


En el tercer ejemplo se muestra la implementacion de estados compuestos.Completo mañana, me voy a cursar :)




