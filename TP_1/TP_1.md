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

A continuacion se muestra una explicación de los ejemplos de diagrama de estado que contiene la biblioteca Sapi. En todos los ejemplos se ha simulado su comportamiento mediante los sieguentes pasos
- 
Como se muestra en la siguiente figura  en los dos primeros ejemplos *toggle.sct* y blink.sct se ha creado diagrama de estados con estados simple, donde se ha tratado, en el primero la creacion de una maquina de estado simple con una solo evento que enciende y apaga un Led en un tiempo determinado por un delay que controla todo el ciclo de scan de programa, en el segundo ejemplo se relacionan dos estados y nuevas constantes que permiten apagar y encender el LED, pero lo mas importante es que se implementa la utilizacion de timers e interrupciones por medio de funciones implementadas en la sapo.h.

Como se muestra en la siguiente figura  el primer ejemplo toggle.sct es un diagrama de estado implementado con yakindu, en este ejemplo primer
![Toggle](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/toggle.PNG)

En el tercer ejemplo se muestra la implementacion de estados compuestos. Finalmente en el tercer ejemplo se agrega la composicion de estados por jeraquia. Como se ve en la siguente imagen el estado





