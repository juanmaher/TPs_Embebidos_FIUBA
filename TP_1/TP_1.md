# **Sistemas Embebidos (86.65) - Trabajo Práctico N°1**
## **Facultad de Ingeniería - U.B.A**

### **Integrantes:**

  *Aranda Cristian - 93631 -  carandac@fi.uba.ar*

  *Hernández Juan Manuel - 102196 - jmhernandez@fi.uba.ar*

  *Meoli Lucas - 102190 - lmeoli@fi.uba.ar*
  
## 1) Guía para compilar y debuggear 

En primer lugar, se debe proceder a instalar el CIAA Launcher, cargar el proyecto firmware_v3 e instalar el plug in de 
Yakindu como se indica los siguientes links:

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

Dentro de la carpeta del ejemplo a compilar, deben existir 2 archivos, un .sct y un .sgen. El archivo con extensión .sct 
es el que contiene la máquina de estado del ejemplo.
El archivo .sgen es el encargado de generar los archivos correspondientes a la máquina de estados. Genera 3 tipos de 
archivos: un \<nombre>.c, un \<nombre>.h y un \<nombre_requiered>.c.
Los dos primeros archivos, corresponde al código generado por Yakindu, y el último archivo indica cuales son las funciones 
que debemos generar nosotros. 

Para Generar el código se debe hacer clic derecho sobre \<nombre>.sgen => Generate Code Artifacts.

![Generate code artifacts](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Generate_code_artifacts.png)

#### 1.3 Compilación y debug

Una vez generado el codigo, se debe compilar haciendo clic en el martillo de arriba a la izquierda o haciendo clic derecho 
sobre el proyecto => Build Project.

Luego, para debagguear se debe hacer clic en la flecha que se encuentra al costado del botón de debug y seleccionar Debug 
Configurations.

![Debug_1](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_1.png)

Al abrirse la nueva pestaña, se debe asegurar que esten configuradas las siguientes opciones:

- Se encuentre seleccionado GDB OpenOCD Debbugging.
- El proyecto sea firmware_v3
- C/C++ Application sea el ejemplo que se desea debaggear.

![Debug_2](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_2.png)

En caso de querer modificar el proyecto se debe hacer clic en Search Project y seleccionar el indicado.

![Debug_3](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Debug_process_3.png)

#### 1.4 Problemas

El principal problema a la hora de instalar las herramientas necesarias para la utilización de la placa EDU-CIAA fue 
lograr el correcto funcionamiento de Yakindu.
En primer lugar, se instalo la última versión disponible (4.0.5) desde el Marketplace, la cual terminó no siendo compatible 
con la versión de eclipse utilizada. 
Para solucionarlo, se recurrió a instalar la versión 3.4.1 realizando los siguientes pasos.

- Click en Help -> Install New Software.
- Desmarcar la opción "Show only the latest versions of available software".
- Ingresar en "Work with" el siguiente link: http://updates.Yakindu.com/statecharts/releases/.
- Esperar que termine la búsqueda.
- Buscar la versin 3.4.1.
- Seleccionar todo las opciones e instalarlo.

![Version_Yakindu](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Version_Yakindu.png)



## 2) Explicación de ejemplos de Yakindu

A continuacion se muestra una explicación de los ejemplos de diagrama de estado que contiene la biblioteca *sapi*. En 
todos los ejemplos se ha simulado su comportamiento como lo indica la siguiente figura:

![simulationStateChart](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/simulationStateChart.png)

### 2.1) Ejemplo 1 - Toggle

El primer ejemplo *toggle.sct* es un diagrama de estados implementado con Yakindu, en este ejemplo se ha definido un solo 
evento que enciende y apaga el LED con un tiempo determinado por un delay. En particular este tiempo controla todo el ciclo 
del programa *main.c*.

![Toggle](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/toggle.PNG)

A partir del evento evTick que ocurre cada TICKRATE_MS se ejecuta la función opLED que apaga o enciende el LED3 dependiendo 
en que estado se encuentre.

#### Funciones de inicialización

- _**boardConfig()**_: Según la placa seleccionada se configuran e inicializan los GPIO.
- _**tickConfig()**_: Se inicializa el Ticks counter según TICKRATE_MS. Se establece el tiempo que tarda en producirse 
  una interrupción.
- _**tickCallbackSet()**_: Se setea que función se llamará cuando se produzca la interrupción.
- _**toggle_init(), toggle_enter()**_: Se inicializa el Statechart.

#### Funciones externas
- _**myTickHook()**_: Cuando se dispara la interrupción de sysTick se setea un Flag: SysTick_Time_Flag.
- _**toggleIface_opLED()**_: Invierte el estado de una salida conectada a un LED.

#### Ciclo while
- El estado del LED es invertido en el programa principal.
    - _**__WFI()**_: El microcontrolador espera una interrupción que lo despierte.

    - Cuando el uC se despierta, el programa verifica el valor de SysTick_Time_Flag y en función de eso continua o no.
        - Resetea el valor de SysTick_Time_Flag.
        - Se produce el evento definido: evTick
        - Se ejecuta un ciclo del diagrama de estados.

    - El uC vuelve a dormirse.

### 2.2) Ejemplo 2 - Blink

El segundo ejemplo, *blink.sct*, es un diagrama de estados simple que invierte el estado de un LED 3 luego de un periodo 
de tiempo usando timers.

![blink](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/blink.PNG)

Para el manejo de los timers se utilizaron los archivos *TimerTicks.h* y *TimerTicks.c* de la biblioteca sapi. Las funciones
definidas estos archivos son utilizadas por las acciones definidas en el código generado por Yakindu.

#### Funciones de inicialización

A las funciones implementadas y utilizadas del ejemplo 1, se les agregan las siguientes:

- _**InitTimerTicks()**_: Inicializa el timer.
- _**blink_init(), blink_enter()**_: Inicializa el Statechart.

#### Funciones externas
- _**blinkIface_opLED()**_: Setea el estado de un LED dado a partir de un valor boolean.
- _**blink_setTimer()**_: Setea el timer a partir de los eventos temporales de la máquina de estados.
- _**blink_unsetTimer()**_: Resetea el timer.

#### Ciclo while
- El estado del LED es invertido en el programa principal.
    - _**__WFI()**_: El microcontrolador espera una interrupción que lo despierte.

    - Cuando el uC se despierta, el programa verifica el valor de SysTick_Time_Flag y en función de eso continua o no.
        - Resetea el valor de SysTick_Time_Flag.
        - Actualiza todos los Timer Ticks.  
        - Recorre todos los Timer Ticks y si hay algún evento pendiente:
          - Se produce el evento.
            - A partir de un valor boolean se setea el estado de un LED.
          - Se marca como ejecutado.
        - Se ejecuta un ciclo del diagrama de estados.

    - El uC vuelve a dormirse.

#### 2.3) Ejemplo 3 - IdleBlink

El diagrama de estados de este ejemplo representa un programa que se encarga de invertir el estado de un LED luego de un
cierto periodo de tiempo. Para esto, se utilizan los eventos temporales de Yakindu y un timer implementado con TimerTicks. 

![idleBlink](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/idleBlink.PNG)

A diferencia del diagrama de estados anterior, este tiene un estado de idle, "reposo". Transcurrido cierto tiempo, se entra en el
estado compuesto, "titila", en el cual se desarrolla una secuencia en la que el LED 3 se enciende y se apaga.

Para el manejo de los timers se utilizó nuevamente la librería TimerTicks que se encuentra en la biblioteca sapi.

#### Funciones de inicialización

#### Funciones externas

#### Ciclo while

#### 2.4 - Ejemplo 4, Buttons


#### 2.5 - Ejemplo 5, Application

## 3) Ejemplos de Trabajo Práctico Final 

### 3.1 - Compostera automática

El proyecto consiste en un dispositivo capaz de sensar y controlar los diferentes parámetros del proceso de compostaje 
para aumentar la velocidad del proceso y reducir la generación de malos olores. La compostera tiene la capacidad de sensar 
la humedad del ambiente y controlarla mediante la utilización de un ventilador y la inyección de agua de un
recipiente externo. También, realiza el control y sensado de la temperatura del proceso. Por otra parte, posee un sensado 
del estado de la tapa para lograr frenar cualquier tipo de control cada vez que el usuario abre la compostera para introducir
material orgánico. Luego de que el usuario depostie material, se activa un motor para poder mezclar el compost e integrar
los nuevos materiales con la mezcla. A continuación se presenta un diagrama de estado de la compostera.

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Principal.png)

Se puede observar que existen 3 maquinas de estados diferentes para controlar los estados de la compostera. A continuación,
se explicara cada maquina de estado por separado, sin embargo antes se mencionarán algunas consideraciones tomadas en cuenta
para la realización de cada máquina.

#### Consideraciones

Para la creación de esta máquina de estado se tuvieron en cuenta algunas consideraciones:

- Que la temperatura y la humedad no tienen relación alguna y se pueden controlar por separado.
- Que la compostera nunca se va a encontrar por debajo de los -10ºC.
- Que el usuario va a depositar materiales aptos para el compostaje.
- Que el usuario únicamente va a abrir la tapa para depositar materiales orgánicos.

#### Humedad

Esta máquina de estados es la encargada del control de humedad del proceso. Existen 3 estados:
- HUMEDECIENDO: estado en el cual la bomba de agua se encuentra encendida y se esta humedeciendo la mezcla. En este caso,
la bomba encencida o apaga de representa con el LED RGB en verde.
- DESHUMEDECIENDO: estado en el cual el ventilador se encuentra encendido para secar el compost. En este caso, el ventilador
encendido o apagado se representa con el LED RGB en azul.
- ESPERANDO: estado en el cual la humedad del compost se encuentra entre el rango ideal por lo cual el ventilador
y la bomba se encuentran apagadas.

Por otra parte, existen 4 eventos posibles:
- siHumedadMenor40: corresponde a que la humedad del compost es menor al 40% por lo cual se debe humectar la mezcla.
- siHumedadMayor60: corresponde a que la humedad del compost es mayor al 60% por lo cual se debe deshumectar la mezcla.
- siHumedadEstable: corresponde a que la humedad del compost se encuentra entre el rango óptimo de humedad.
- siAberturaTapa: corresponde a que la tapa se encuentra abierta, por lo cual se debe frenar cualquier proceso de control
de humedad ya que el usuario se encuentra depositando material orgánico.

#### Temperatura

Esta máquina de estados es la encargada del control de temperatura del proceso. En este caso, solo se controlan las altas 
temperatura que son las mas perjudiciales para lograr un correcto proceso de compostaje. Existen 2 estados:
- ENFRIANDO: estado en el cual el ventilador se encuentra encendido para enfriar el compost. En este caso, el ventilador 
encendido o apagado se representa con el LED RGB en azul.
- ESPERANDO: estado en el cual la temperatura del compost se encuentra por debajo del umbral máximo de temperatura ideal 
por lo cual el ventilador se encuentra apagado.

Por otra parte, existen 3 eventos posibles:
- siTemperaturaMayor60: corresponde a que la temperatura del compost es mayor al 60°C por lo cual se debe enfriar la mezcla.
- siTemperaturaEstable: corresponde a que la temperatura del compost se encuentra entre el rango óptimo de temperatura.
- siAberturaTapa: corresponde a que la tapa se encuentra abierta, por lo cual se debe frenar cualquier proceso de control
de temperatura ya que el usuario se encuentra depositando material orgánico.

#### Compostar

Esta máquina de estados es la encargada de controlar el proceso cuando el usuario abre la tapa para depositar materiales
orgánicos. Existen 4 estados:
- RELLENANDO: estado en el cual el usuario abrió la tapa y se encuentra depositando materiales orgánicos. En este caso,
el estado de la tapa se representa mediante el encendio o apagado LED3.
- SONANDO: estado en el cual el usuario lleva mucho tiempo la tapa abierta o se olvidó la tapa abierta y comienza a sonar
una alarma para avisar que la debe cerrar. Este caso se representa con el LED1 prendido y se apaga el led indicador de 
la tapa abierta LED3.
- MEZCLANDO: estado en el cual se enciende el motor para mezclar el compost una vez que el usuario ya depositó el material
orgánico. En ese caso, el encendido y apagado del motoro se representa con el encendido y apagado del LED2.
- ESPERANDO: estado en el cual el motor se encuentra apagado hasta que el usuario deposite material orgánico y cierre la tapa.

Por otra parte, existen 2 eventos posible:
- siAberturaTapa: corresponde a la apertura de tapa por parte del usuario.
- siCerradoTapa: Corresponde a que la tapa se encuentra cerrada luego de depostiar material orgánico.

##### Pulsadores
Todos los eventos mencionados en las máquinas de estados van a ser activados por la utilización de los pulsadores de la 
placa EDU-CIAA. Para ello, dado la cantidad de estados, cada pulsador va a ser el encargado de manejar dos eventos como se 
muestra en los siguientes diagramas.

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Pulsadores.png)

Como se puede observar, cada pulsador activa un evento al mantenerse presionado o al estar sin presionar. En la siguiente tabla
se especifica la acción que representa cada pulsador.

|Pulsador                    | Presionado                 | Sin presionar              |
|----------------------------|----------------------------|----------------------------|
|TEC1                        | Abrir tapa                 | Cerrar tapa                |
|TEC2                        | Humedad mayor al 60%       | Humedad estable            |
|TEC3                        | Humedad menor al 40%       | Humedad estable            |
|TEC4                        | Temperatura mayor a 60ºC   | Temperatura estable        |

###### Complicaciones con pulsador 1

Como se puede observar en las máquinas de estados, el pulsador 1 no tiene un debounce como el resto de los pulsadores. Esto
se realizó de esta forma para evitar un bug que estabamos teniendo a la hora de presionar el pulsador 1, el cual interrumpe
los otros procesos de control, y luego se presionaba otro pulsador. Esto provocaba que si se soltaba el pulsador 1, pero se
seguía presionando el otro pulsador, este se comportaba como si se estuviera presionando el pulsador 1. Mediante esta solución,
logramos obtener el resultado esperado, el cual tambien se asemeja mas a la realidad, dado que el sensor de abertura y cerrado
de tapa no dispone de ningun debounce.

#### Main

Al igual que en los ejemplos analizados anteriormente, existen funciones generadas por Yakindu y otras que se deben generar.

