# **Sistemas Embebidos (86.65) - Trabajo Práctico N°2**
## **Facultad de Ingeniería - U.B.A**

### **Integrantes:**

  *Aranda Cristian - 93631 -  carandac@fi.uba.ar*

  *Hernández Juan Manuel - 102196 - jmhernandez@fi.uba.ar*

  *Meoli Lucas - 102190 - lmeoli@fi.uba.ar*
  
## 1) Migración del proyecto app.c
Para la correcta migración del proyecto app.c se copio la carpeta *firmware_v3\examples\c\app*, en *\firmware_v3\projects\TP2*, luego se procedió a cambiar los nombres del de los archivos *app.c* y *app.h* por *tp2.c* y *tp.h* respectivamente. Luego en el archivo tp2.h se reescribió 
```C
#ifndef _APP_H_
#define _APP_H_
```
por 

```C
#ifndef _TP2_H_
#define _TP2_H_
```
para luego realizar un include en *tp2.c*. Con estos pasos realizamos una migración exitosa.

A continuación se detallan las funciones útiles de la librería sapi para el parpadeo de un led y un *printf* a través de la *UART_USB*.

- *boardConfig()*:  Inicializa y configura todos los pines de la EDU-CIAA como GPIO.
- *delay(n )*: Retardo bloqueante que dura *n* milisegundos
- *bool_t gpioRead( gpioMap_t pin )*:  Leé el valor de lo que está conectado a *pin*, por ejemplo TEC4.
- *bool_t gpioWrite( gpioMap_t pin, bool_t value )*: Escribe el valor de *value* en *pin*, por ejemplo LEDG.
- *bool_t gpioToggle( gpioMap_t pin )*: Alterna el estado de *pin*, por ejemplo invierte el valor de un LEDG.
- *printf() que utiliza int printf_(const char* format, ...)*: Imprime un string a través de la *UART_USB*, para lo que se debe tener conectado a una terminal por puerto serie donde se pueda visualizar los datos.




## 2) Implementación de funciones para el manejo de los GPIO's

Para la implementación de nuestras funciones para el manjeo de los GPIOs se debió entender el funcionamiento de cada una de ellas. A continuación se explicarán los conceptos principales de las siguientes funciones:  *gpioInit()*, *gpioWrite()* y *gpioRead()*.

#### gpioInit()

Es la función que inicializa el pin utilizado como GPIO. En la familia de chips LPC43xx esta instrucción consiste en dos partes:

i. Configurar eléctricamente el pin a utilizar a través del periférico System Control Unit (SCU).

ii. Configurar el modo de uso del GPIO a través de sus propios registros en el memory mapping. 

La función esta definida como: **bool_t gpioInit( gpioMap_t pin, gpioInit_t config );**

El primer parámetro es *pin* que es un tipo enumerativo gpioMap_t, definido en sapi_peripheral_map.h, en donde distintos puertos 
son representados por un número.

```C
typedef enum {
      VCC = -2, GND = -1,
      // P1 header
      T_FIL1,    T_COL2,    T_COL0,    T_FIL2,      T_FIL3,  T_FIL0,     T_COL1,
      CAN_TD,    CAN_RD,    RS232_TXD, RS232_RXD,
      // P2 header
      GPIO8,     GPIO7,     GPIO5,     GPIO3,       GPIO1,
      LCD1,      LCD2,      LCD3,      LCDRS,       LCD4,
      SPI_MISO,
      ENET_TXD1, ENET_TXD0, ENET_MDIO, ENET_CRS_DV, ENET_MDC, ENET_TXEN, ENET_RXD1,
      GPIO6,     GPIO4,     GPIO2,     GPIO0,
      LCDEN,
      SPI_MOSI,
      ENET_RXD0,
      // Switches
      // 36   37     38     39
      TEC1,  TEC2,  TEC3,  TEC4,
      // Leds
      // 40   41     42     43     44     45
      LEDR,  LEDG,  LEDB,  LED1,  LED2,  LED3,
} gpioMap_t;
```

El segundo parámetro es *config* que es un tipo enumerativo gpioInit_t, definido en sapi_gpio.h, que representa los estados en los cuales 
se puede inicializar un puerto. 

```C
typedef enum {
   GPIO_INPUT, GPIO_OUTPUT,
   GPIO_INPUT_PULLUP, GPIO_INPUT_PULLDOWN,
   GPIO_INPUT_PULLUP_PULLDOWN,
   GPIO_ENABLE
} gpioInit_t;
```

A continuación se muestra las principales funciones que componen a gpioInit:    

```C
bool_t gpioInit( gpioMap_t pin, gpioInit_t config )
{
   if( pin == VCC ){
	  return FALSE;
   }
   if( pin == GND ){
	  return FALSE;
   }

   bool_t ret_val     = 1;

   int8_t pinNamePort = 0;
   int8_t pinNamePin  = 0;

   int8_t func        = 0;

   int8_t gpioPort    = 0;
   int8_t gpioPin     = 0;

   gpioObtainPinInit( pin, &pinNamePort, &pinNamePin, &func,
                      &gpioPort, &gpioPin );

   switch(config) {

   case GPIO_ENABLE:
      /* Initializes GPIO */
      Chip_GPIO_Init(LPC_GPIO_PORT);
      break;

   case GPIO_INPUT:
      Chip_SCU_PinMux(
         pinNamePort,
         pinNamePin,
         SCU_MODE_INACT | SCU_MODE_INBUFF_EN | SCU_MODE_ZIF_DIS,
         func
      );
      Chip_GPIO_SetDir( LPC_GPIO_PORT, gpioPort, ( 1 << gpioPin ), GPIO_INPUT );
      break;

   case GPIO_INPUT_PULLUP:
      Chip_SCU_PinMux(
         pinNamePort,
         pinNamePin,
         SCU_MODE_PULLUP | SCU_MODE_INBUFF_EN | SCU_MODE_ZIF_DIS,
         func
      );
      Chip_GPIO_SetDir( LPC_GPIO_PORT, gpioPort, ( 1 << gpioPin ), GPIO_INPUT );
      break;

   case GPIO_INPUT_PULLDOWN:
      Chip_SCU_PinMux(
         pinNamePort,
         pinNamePin,
         SCU_MODE_PULLDOWN | SCU_MODE_INBUFF_EN | SCU_MODE_ZIF_DIS,
         func
      );
      Chip_GPIO_SetDir( LPC_GPIO_PORT, gpioPort, ( 1 << gpioPin ), GPIO_INPUT );
      break;
   case GPIO_INPUT_PULLUP_PULLDOWN:
      Chip_SCU_PinMux(
         pinNamePort,
         pinNamePin,
         SCU_MODE_REPEATER | SCU_MODE_INBUFF_EN | SCU_MODE_ZIF_DIS,
         func
      );
      Chip_GPIO_SetDir( LPC_GPIO_PORT, gpioPort, ( 1 << gpioPin ), GPIO_INPUT );
      break;

   case GPIO_OUTPUT:
      Chip_SCU_PinMux(
         pinNamePort,
         pinNamePin,
         SCU_MODE_INACT | SCU_MODE_ZIF_DIS | SCU_MODE_INBUFF_EN,
         func
      );
      Chip_GPIO_SetDir( LPC_GPIO_PORT, gpioPort, ( 1 << gpioPin ), GPIO_OUTPUT );
      Chip_GPIO_SetPinState( LPC_GPIO_PORT, gpioPort, gpioPin, 0);
      break;

   default:
      ret_val = 0;
      break;
   }

   return ret_val;

}

```

Como se puede observar, luego de inicialziar las variables se llama a la funcion **pioObtainPinInit( pin, &pinNamePort, 
&pinNamePin, &func, &gpioPort, &gpioPin )**, en la cual a partir del arreglo constante *gpioPinsInit[]* se obtiene
las características de cada puerto.

```C
const pinInitGpioLpc4337_t gpioPinsInit[] = {

   //{ {PinNamePortN ,PinNamePinN}, PinFUNC, {GpioPortN, GpioPinN} }
     
      { {4, 1}, FUNC0, {2, 1} },   //   0   CON1_36   T_FIL1          
      { {7, 5}, FUNC0, {3,13} },   //   1   CON1_34   T_COL2          

      { {1, 5}, FUNC0, {1, 8} },   //   2   CON1_39   T_COL0          
      { {4, 2}, FUNC0, {2, 2} },   //   3   CON1_37   T_FIL2          
      { {4, 3}, FUNC0, {2, 3} },   //   4   CON1_35   T_FIL3          
      { {4, 0}, FUNC0, {2, 0} },   //   5   CON1_33   T_FIL0          
      { {7, 4}, FUNC0, {3,12} },   //   6   CON1_31   T_COL1          

      { {3, 2}, FUNC4, {5, 9} },   //   7   CON1_29   CAN_TD          
      { {3, 1}, FUNC4, {5, 8} },   //   8   CON1_27   CAN_RD          

      { {2, 3}, FUNC4, {5, 3} },   //   9   CON1_25   RS232_TXD       
      { {2, 4}, FUNC4, {5, 4} },   //  10   CON1_23   RS232_RXD       

      { {6,12}, FUNC0, {2, 8} },   //  11   CON2_40   GPIO8           
      { {6,11}, FUNC0, {3, 7} },   //  12   CON2_38   GPIO7           
      { {6, 9}, FUNC0, {3, 5} },   //  13   CON2_36   GPIO5           
      { {6, 7}, FUNC4, {5,15} },   //  14   CON2_34   GPIO3           
      { {6, 4}, FUNC0, {3, 3} },   //  15   CON2_32   GPIO1           

      { {4, 4}, FUNC0, {2, 4} },   //  16   CON2_30   LCD1            
      { {4, 5}, FUNC0, {2, 5} },   //  17   CON2_28   LCD2            
      { {4, 6}, FUNC0, {2, 6} },   //  18   CON2_26   LCD3            
      { {4, 8}, FUNC4, {5,12} },   //  19   CON2_24   LCDRS           
      { {4,10}, FUNC4, {5,14} },   //  20   CON2_22   LCD4            

      { {1, 3}, FUNC0, {0,10} },   //  21   CON2_18   SPI_MISO        

      { {1,20}, FUNC0, {0,15} },   //  22   CON2_16   ENET_TXD1       
      { {1,18}, FUNC0, {0,13} },   //  23   CON2_14   ENET_TXD0       
      { {1,17}, FUNC0, {0,12} },   //  24   CON2_12   ENET_MDIO       
      { {1,16}, FUNC0, {0, 3} },   //  25   CON2_10   ENET_CRS_DV      
      { {7, 7}, FUNC0, {3,15} },   //  26   CON2_08   ENET_MDC         
      { {0, 1}, FUNC0, {0, 1} },   //  27   CON2_06   ENET_TXEN        
      { {0, 0}, FUNC0, {0, 0} },   //  28   CON2_04   ENET_RXD1        

      { {6,10}, FUNC0, {3, 6} },   //  29   CON2_35   GPIO6            
      { {6, 8}, FUNC4, {5,16} },   //  30   CON2_33   GPIO4            
      { {6, 5}, FUNC0, {3, 4} },   //  31   CON2_31   GPIO2            
      { {6, 1}, FUNC0, {3, 0} },   //  32   CON2_29   GPIO0            

      { {4, 9}, FUNC4, {5,13} },   //  33   CON2_23   LCDEN            

      { {1, 4}, FUNC0, {0,11} },   //  34   CON2_21   SPI_MOSI         

      { {1,15}, FUNC0, {0, 2} },   //  35   CON2_09   ENET_RXD0       

      { {1, 0}, FUNC0, {0, 4} },   // 36   TEC1    TEC_1  
      { {1, 1}, FUNC0, {0, 8} },   // 37   TEC2    TEC_2  
      { {1, 2}, FUNC0, {0, 9} },   // 38   TEC3    TEC_3  
      { {1, 6}, FUNC0, {1, 9} },   // 39   TEC4    TEC_4  

      { {2, 0}, FUNC4, {5, 0} },   // 43   LEDR    LED0_R 
      { {2, 1}, FUNC4, {5, 1} },   // 44   LEDG    LED0_G 
      { {2, 2}, FUNC4, {5, 2} },   // 45   LEDB    LED0_B 
      { {2,10}, FUNC0, {0,14} },   // 40   LED1    LED1   
      { {2,11}, FUNC0, {1,11} },   // 41   LED2    LED2   
      { {2,12}, FUNC0, {1,12} },   // 42   LED3    LED3   
};
```
Por ejemplo al invocar la función con *gpioInit( GPIO5, GPIO_INPUT );*, donde se inicializa al GPIO5 como entrada, lo que sucede
es que GPIO5 = 13, el cual corresponde a gpioPinsInit[13] que contiene los siguientes atributos:

- PinNamePortN = 6
- PinNamePinN = 9
- PinFUNC = FUNC0
- GpioPortN = 3
- GpioPinN = 5

Se puede ver en la siguiente imagen que coincide con la hoja de datos. De esta forma queda realizado el mapeo del GPIO con la placa.

![Distribucion de carpetas y archivos.](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_2/imagenes_tp2/GPIO5.png)

Luego, a partir de las funciónnes *Chip_SCU_PinMux()* y  *Chip_GPIO_SetDir()* se configura la función y la dirección del puerto 
dependiendo el estado en el que se quiera setear.


#### gpioWrite()

Es la función que escribe un valor binario en el pin del GPIO cuando está configurado como salida. La función esta definida como: 
**bool_t gpioWrite( gpioMap_t pin, bool_t value )** donde gpioMap_t coincide con lo explicado anteriormente y *value* corresponde
al valor en el cual se quiere setear el pin (VCC o GND). A continuación, se muestra el contenido de la función.

```C
bool_t gpioWrite( gpioMap_t pin, bool_t value )
{
   if( pin == VCC ){
	  return FALSE;
   }
   if( pin == GND ){
	  return FALSE;
   }

   bool_t ret_val     = 1;

   int8_t pinNamePort = 0;
   int8_t pinNamePin  = 0;

   int8_t func        = 0;

   int8_t gpioPort    = 0;
   int8_t gpioPin     = 0;

   gpioObtainPinInit( pin, &pinNamePort, &pinNamePin, &func, &gpioPort, &gpioPin );
   Chip_GPIO_SetPinState( LPC_GPIO_PORT, gpioPort, gpioPin, value);

   return ret_val;
}

```
Al igual que en el caso anterior, luego de inicializar las variables, se obtiene realiza el mapeo mediante la función 
*gpioObtainPinInit()*. Luego, se setea el estado del pin mediante *Chip_GPIO_SetPinState()*, en donde se escribe la 
dirección de memoria correspondiente a la configuración del pin.

#### gpioRead()

Es la función que lee el valor del pin utilizado como GPIO, la cual está definida como **bool_t gpioRead( gpioMap_t pin )**.
En este caso, solo tiene un parámetro que corresponde al pin que se desea leer.

```C
bool_t gpioRead( gpioMap_t pin )
{
   if( pin == VCC ){
      return TRUE;
   }
   if( pin == GND ){
      return FALSE;
   }

   bool_t ret_val     = OFF;

   int8_t pinNamePort = 0;
   int8_t pinNamePin  = 0;

   int8_t func        = 0;

   int8_t gpioPort    = 0;
   int8_t gpioPin     = 0;

   gpioObtainPinInit( pin, &pinNamePort, &pinNamePin, &func,
                      &gpioPort, &gpioPin );

   ret_val = (bool_t) Chip_GPIO_ReadPortBit( LPC_GPIO_PORT, gpioPort, gpioPin );

   return ret_val;
}
```
Como se puede observar, la función es practicamente igual a gpioWrite(), con la diferencia de que ahora se llama 
*Chip_GPIO_ReadPortBit()* para leer de la memoria el estado del pin.


#### Creacion de nuetras librerías

![Distribucion de carpetas y archivos.](https://github.com/juanmaher/TPs_Embebidos_FIUBA/blob/main/TP_2/imagenes_tp2/Tp2_archivos.png)




## 3) Implementación para el trabajo final


