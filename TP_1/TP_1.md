# **Sistemas Embebidos (86.65) - Trabajo Práctico N°1**
## **Facultad de Ingeniería - U.B.A**

### **Integrantes:**

  *Aranda Cristian - 93631 -  carandac@fi.uba.ar*

  *Hernández Juan Manuel - 102196 - jmhernandez@fi.uba.ar*

  *Meoli Lucas - 102190 - lmeoli@fi.uba.ar*
  
## 1) Guía para compilar y debuguear 

En primer lugar, se debe proceder a instalar el CIAA Launcher, cargar el proyecto firmware_v3 e instalar el plug in de Yakindu como se indica los siguientes links:

- https://github.com/epernia/software.
- https://drive.google.com/drive/folders/1zd6Gdea687fpQ7gfa3lu9GBVB7Hph5qz
- https://github.com/epernia/firmware_v3/blob/master/documentation/firmware/eclipse/usage-es.md

Una vez realizados las indicaciones anteriores, se debe conectar la placa EDU-CIAA y realizar los siguientes pasos:

#### 1.1 - Selección de programa y placa

En el archivo Makefile se configura la placa a utilizar y el programa a compilar: 

- BOARD = edu_ciaa_nxp
- PROGRAM_PATH = <Direccion del ejemplo a compilar>
- PROGRAM_NAME = <Nombre del ejemplo a compilar>