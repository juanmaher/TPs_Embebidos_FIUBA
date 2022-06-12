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

## 3) Implementación para el trabajo final


