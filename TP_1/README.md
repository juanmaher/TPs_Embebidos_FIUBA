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
Los dos primeros archivos corresponden al código generado por Yakindu y el último archivo indica cuales son las funciones 
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
En primer lugar, se instaló la última versión disponible (4.0.5) desde el Marketplace, la cual terminó no siendo compatible 
con la versión de eclipse utilizada. 
Para solucionarlo, se recurrió a instalar la versión 3.4.1 realizando los siguientes pasos.

- Click en Help -> Install New Software.
- Desmarcar la opción "Show only the latest versions of available software".
- Ingresar en "Work with" el siguiente link: http://updates.Yakindu.com/statecharts/releases/.
- Esperar que termine la búsqueda.
- Buscar la versin 3.4.1.
- Seleccionar todas las opciones e instalarlo.

![Version_Yakindu](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Version_Yakindu.png)



## 2) Explicación de ejemplos de Yakindu

A continuación se muestra una explicación de los ejemplos de diagrama de estado que contiene la biblioteca *sapi*. En 
todos los ejemplos se ha simulado su comportamiento como lo indica la siguiente figura:

![simulationStateChart](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/simulationStateChart.png)

### 2.1 Ejemplo 1 - Toggle

El primer ejemplo *toggle.sct* es un diagrama de estados implementado con Yakindu, en este ejemplo se ha definido un solo 
evento que enciende y apaga el LED con un tiempo determinado por un delay. En particular este tiempo controla todo el ciclo 
del programa *main.c*.

![Toggle](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/toggle.PNG)

A partir del evento evTick que ocurre cada TICKRATE_MS se ejecuta la función opLED que apaga o enciende el LED3 dependiendo 
en que estado se encuentre.

#### 2.1.1 Funciones de inicialización

- _**boardConfig()**_: Según la placa seleccionada se configuran e inicializan los GPIO.
- _**tickConfig()**_: Se inicializa el Ticks counter según TICKRATE_MS. Se establece el tiempo que tarda en producirse 
  una interrupción.
- _**tickCallbackSet()**_: Se setea que función se llamará cuando se produzca la interrupción.
- _**toggle_init(), toggle_enter()**_: Se inicializa el Statechart.

#### 2.1.2 Funciones externas
- _**myTickHook()**_: Cuando se dispara la interrupción de sysTick se setea un Flag: SysTick_Time_Flag.
- _**toggleIface_opLED()**_: Invierte el estado de una salida conectada a un LED.

#### 2.1.3 Ciclo while
- El estado del LED es invertido en el programa principal.
    - _**__WFI()**_: El microcontrolador espera una interrupción que lo despierte.

    - Cuando el uC se despierta, el programa verifica el valor de SysTick_Time_Flag y en función de eso continua o no.
        - Resetea el valor de SysTick_Time_Flag.
        - Se produce el evento definido: evTick
        - Se ejecuta un ciclo del diagrama de estados.

    - El uC vuelve a dormirse.



### 2.2 Ejemplo 2 - Blink

El segundo ejemplo, *blink.sct*, es un diagrama de estados simple que invierte el estado de un LED 3 luego de un periodo 
de tiempo usando timers.

![blink](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/blink.PNG)

Para el manejo de los timers se utilizaron los archivos *TimerTicks.h* y *TimerTicks.c* de la biblioteca sapi. Las funciones
definidas estos archivos son utilizadas por las acciones definidas en el código generado por Yakindu.

#### 2.2.1 Funciones de inicialización

A las funciones implementadas y utilizadas del ejemplo 1, se les agregan las siguientes:

- _**InitTimerTicks()**_: Inicializa el timer.
- _**blink_init(), blink_enter()**_: Inicializa el Statechart.

#### 2.2.2 Funciones externas
- _**blinkIface_opLED()**_: Setea el estado de un LED dado a partir de un valor boolean.
- _**blink_setTimer()**_: Setea el timer a partir de los eventos temporales de la máquina de estados.
- _**blink_unsetTimer()**_: Resetea el timer.

#### 2.2.3 Ciclo while
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



### 2.3 Ejemplo 3 - IdleBlink

El diagrama de estados de este ejemplo representa un programa que se encarga de invertir el estado de un LED luego de un
cierto periodo de tiempo. Para esto, se utilizan los eventos temporales de Yakindu y un timer implementado con TimerTicks. 

![idleBlink](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/idleBlink.PNG)

A diferencia del diagrama de estados anterior, este tiene un estado de idle, "reposo". Transcurrido cierto tiempo, se entra en el
estado compuesto, "titila", en el cual se desarrolla una secuencia en la que el LED 3 se enciende y se apaga.

Para el manejo de los timers se utilizó nuevamente la librería TimerTicks que se encuentra en la biblioteca sapi.

#### 2.3.1 Funciones de inicialización

Las funciones que se llaman al iniciar el programa son las mismas que en los ejemplos anteriores.

#### 2.3.2 Funciones externas

Los funciones que se desarrollaron para este ejemplo son iguales o equivalentes a las utilizadas en los ejemplos anteriores.

#### 2.3.3 Ciclo while

El ciclo while de este ejemplo es equivalente al del ejemplo 2.



### 2.4 Ejemplo 4 - Buttons

El diagrama de estados de este ejemplo representa el funcionamiento de un programa que permite encender o apagar un LED
con un pulsador. Cuando se aprieta se dispara un evento evTECXOprimido y se pasa del estado NO_OPRIMIDO al estado
DEBOUNCE. Este evento se dispara apretando cualquiera de los 4 botenes. Luego, se inicia un timer que pasados 100 ms se 
dispara un evento que hace que se avance al evento VALIDACION. Esto se implementa para evitar el rebote del pulsador, ya
que si en este estado el pulsador no sigue apretado, se disparará el evento evTECXNoOprimido y se volverá al estado 
NO_OPRIMIDO. En caso contrario, se disapara el evento evTECXOprimido nuevamente y se pasará al estado OPRIMIDO. Una vez
en este estado se ejecutará la acción entry y se ejecutará la función opLED() que invertirá el estado del LED 3. Además,
se inicializará la variable entera viTecla con el número de botón que se ha pulsado. Esta información se extrae del evento
evTECXOprimido. Cuando se suelte el botón, se pasa al estado NO_OPRIMIDO. 

![Buttons](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/Buttons.PNG)

Para el manejo de los timers se utilizó nuevamente la librería TimerTicks que se encuentra en la biblioteca sapi.

#### 2.4.1 Funciones de inicialización

Las funciones que se llaman al iniciar el programa son las mismas que en los ejemplos anteriores.

#### 2.4.2 Funciones externas
- _**buttonsIface_opLED()**_: Invierte el estado de una salida conectada a un LED.
- _**Buttons_GetStatus\_()**_: Devuelve un entero que representa en que estado se encuentran los 4 pulsadores a partir 
  de la lectura de cada GPIO.

#### 2.4.3 Ciclo while
- El estado del LED es invertido en el programa principal.
    - _**__WFI()**_: El microcontrolador espera una interrupción que lo despierte.

    - Cuando el uC se despierta, el programa verifica el valor de SysTick_Time_Flag y en función de eso continua o no.
        - Resetea el valor de SysTick_Time_Flag.
        - Actualiza todos los Timer Ticks.
        - Recorre todos los Timer Ticks y si hay algún evento pendiente:
            - Se produce el evento.
            - Se marca como ejecutado.
        - Se obtiene el estado de los pulsadores.
          - Si algún o algunos pulsadores están apretados, se ejecuta el evento evTECXOprimodo.
          - En caso contrario, se ejecuta el evento evTECXNoOprimodo.
        - Se ejecuta un ciclo del diagrama de estados.

    - El uC vuelve a dormirse.
    


### 2.5 Ejemplo 5 - Application

El diagrama de estados implementado para este ejemplo representa un programa que se encarga de encender, apagar y hacer
parpadear LEDs según los botones que se pulsen. Para esto se utilizan timers y los eventos temporales de Yakindu. Además,
se agrega un antirrebote para que haya una correcta interpretación de los estados de los botones. Todo esto se compone en tres regiones que trabajan de forma cuasi concurrente.


El Statechart de este ejemplo incorpora cosas de los ejemplos anteriores. Para la interpretación de los botones utiliza
los mismos estados y eventos del ejemplo 4. A diferencia del ejemplo anterior, se le da importancia a que botón fue pulsado, cuando se entra al estado OPRIMIDO se realizar un raise al evento interno, siTECXOK, y se guarda en viTecla el valor que tomo el evento evTECXOprimido. De esta forma podemos obtener las distintas acciones en función:

- Con la tecla 1 se apagan todos los LEDs y se setea el evento interno siNoTitilarLED. 
- Con la tecla 2 se enciende el LED 1.
- Con la tecla 3 se enciende el LED 2.
- Con la tecla 4 se setea el evento interno siTitilarLED.

Este diagrama incorpora los estados del ejemplo 3 que hacen parpadear el LED 3 con la diferencia de que ahora se pasa
al estado compuesto, TITILAR, cuando se dispara a traves de un *raise* el evento interno siTitilarLED y no luego de un tiempo establecido. Dentro de
este estado compuesto se pasa del estado APAGADO al ENCENDIDO y viceversa cada cierto tiempo establecido. Para esto se
utiliza el TimerTicks de la biblioteca sapi. En el estado APAGADO se ejecuta la función opLED() que apagado el LED 3 y 
analogamente en el estado ENCENDIDO se ejecuta la misma función que lo enciende. Cuando se ejecute el evento siNoTitilarLED por medio de otro *raise*
se sale del estado TITILAR y se vuelve al estado REPOSO. Este es el primer ejemplo donde se trabaja con condiciones de guarda en las transiciones del estado main region, ya que para cada valor obtenido del pulsador que se guarda en *viTecla*, se compara el valor de esta variables con las cuatro teclas en orden decreciente empezando por la tecla 4.

![Application](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/ImagenesEjemploStateCharts/Application.PNG)

Para el manejo de los timers se utilizó nuevamente la librería TimerTicks que se encuentra en la biblioteca sapi.

#### 2.5.1 Funciones de inicialización

Las funciones que se llaman al iniciar el programa son las mismas que en los ejemplos anteriores.

#### 2.5.2 Funciones externas
- _**applicationIface_opLED()**_: Setea el estado de un LED dado a partir de un valor boolean.

#### 2.5.3 Ciclo while

El ciclo while de este ejemplo es equivalente al del ejemplo 4.



## 3) Ejemplos de Trabajo Práctico Final 

### 3.1 - Compostera automática

El proyecto consiste en un dispositivo capaz de sensar y controlar los diferentes parámetros del proceso de compostaje 
para aumentar la velocidad del proceso y reducir la generación de malos olores. La compostera tiene la capacidad de sensar 
la humedad del ambiente y controlarla mediante la utilización de un ventilador y la inyección de agua de un
recipiente externo. También, realiza el control y sensado de la temperatura del proceso. Por otra parte, posee un sensado 
del estado de la tapa para lograr frenar cualquier tipo de control cada vez que el usuario abre la compostera para introducir
material orgánico. Luego de que el usuario deposite el material, se activa un motor para poder mezclar el compost e integrar
los nuevos materiales con la mezcla.
 
A continuación, se explicará cada máquina de estado por separado, sin embargo antes se mencionarán algunas consideraciones 
tomadas en cuenta para la realización de cada máquina.

#### Consideraciones

- La temperatura y la humedad no tienen relación alguna y se pueden controlar por separado.
- La compostera nunca se va a encontrar por debajo de los -10ºC.
- El usuario va a depositar materiales aptos para el compostaje.
- El usuario únicamente va a abrir la tapa para depositar materiales orgánicos.
- No se van a desarrollar dos procesos de control al mismo tiempo. Es decir, si se tiene que controlar temperatura
y humedad, primero se controlará una y después la otra.

#### Humedad

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Humedad.png)

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

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Temperatura.png)

Esta máquina de estados es la encargada del control de temperatura del proceso. En este caso, solo se controlan las altas 
temperatura que son las mas perjudiciales para lograr un correcto proceso de compostaje. Existen 2 estados:
- ENFRIANDO: estado en el cual el ventilador se encuentra encendido para enfriar el compost. En este caso, el ventilador 
encendido o apagado se representa con el LED RGB en rojo.
- ESPERANDO: estado en el cual la temperatura del compost se encuentra por debajo del umbral máximo de temperatura ideal 
por lo cual el ventilador se encuentra apagado.

Por otra parte, existen 3 eventos posibles:
- siTemperaturaMayor60: corresponde a que la temperatura del compost es mayor al 60°C por lo cual se debe enfriar la mezcla.
- siTemperaturaEstable: corresponde a que la temperatura del compost se encuentra entre el rango óptimo de temperatura.
- siAberturaTapa: corresponde a que la tapa se encuentra abierta, por lo cual se debe frenar cualquier proceso de control
de temperatura ya que el usuario se encuentra depositando material orgánico.

#### Compostar

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Compostar.png)

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
se realizó de esta forma para evitar un bug que estabamos teniendo a la hora de presionar el pulsador 1 (el cual interrumpe
los otros procesos de control) y luego se presionaba otro pulsador. Esto provocaba que si se soltaba el pulsador 1, pero se
seguía presionando el otro pulsador, este se comportaba como si se estuviera presionando el pulsador 1. Mediante esta solución,
logramos obtener el resultado esperado, el cual también se asemeja más a la realidad dado que en el sensor de abertura y cerrado
de tapa no se debe implementar ningún debounce.

###### Loop pulsadores 2, 3 y 4

A partir de los diagramas de estados se puede observar que, a diferencia del pulsador 1, los pulsador 2, 3 y 4 en el estado
OPRIMIDO tiene un "loop" en el cual el evento ecTECXOprimido los vuelve a llevar al mismo estado. Esto se implementó de 
esta forma para lograr que, si algún pulsador se mantiene presionado y en el medio se presiona el pulsador 1, una vez que
se suelta el pulsador 1, el proceso de control que se estaba llevando acabo anteriormente continue sin la necesidad de 
volver a presionar el pulsador, ya que este se encuentra presionado. Un ejemplo que es equivalemte a esta sitación es
si se encuentra encendido el ventilador para bajar la temperatura de la mezcla pero en el medio se abre la tapa.
En ese caso, el ventilador se debería apagarse hasta cerrar la tapa y luego volverse a encender si la temperatura sigue
estando por encima de los 60°C.

##### Máquina de estado completa de la compostera
A continuación se puede observar el diagrama completo de la compostera.
![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Compostera_Diagrama_Principal.png)

#### Main

El código para implementar la compostera esta basado en el ejercicio 5 (application) por lo cual gran parte es igual a lo ya
explicado anteriormente. Por este motivo, se hará hincapié en la parte de código modificada respecto a dicho ejercicio.

![Diagrama_Compostera](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_1/Imagenes_TP_1/Codigo_switch_compostera.png)

En primer lugar, se puede observar que se implementó un switch para poder diferenciar los 4 pulsadores ya que ahora cada 
uno tiene una funcion específica correspondiente. Además, existe una condición especial para el pulsador 1, ya que es el 
encargado del manejar el estado de la tapa, la cual si se encuentra abierta se deben parar todos los procesos de control
que se estaban llevando a cabo.

#### Resultados
De esta forma, a partir de las máquinas de estados realizadas y las modificaciones implementadas en el main.c se obtuvo el
comportamiento deseado, es decir:

- Cada pulsador controla el evento especificado simulando una compostera real.
- Al presionar el pulsador 1 el resto de los pulsadores dejan de tener efecto ya que la "tapa se encuentra abierta".
- Los pulsadores 2, 3 y 4, solo se pueden activar cuando la tapa se encuetra cerrada (esto incluye la etapa de mezclado).
