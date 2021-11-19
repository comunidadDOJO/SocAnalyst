---
title: "Infection Monkey"
author:
    name: "Leonardo Uzcátegui"
    url: https://leonuz.github.io
date: 11/11/2021
---
![InfectionMonkey](img/im1.png)

# Emulación de Adversarios utilizando Infection Monkey

# ¿Qué es Infection Monkey?  
**Infection Monkey** es una herramienta de pruebas de ciberseguridad, capaz de infiltarse en un centro de datos en busca de vulnerabilidades y explotarlas. Se puede instalar en una máquina virtual en cualquier parte dentro del centro de datos, y desde alli, Infection Monkey intentará encontar posibles fallos de seguridad. Su comportamiento es más parecido a como un ciberdelincuente opera, que como un escáner de vulnerabilidades. Infection Monkey intentara recorrer el centro de datos aprovechando diferentes tecnicas de movimiento lateral típicos de un atacante real que ya ha comprometido un sistema interno. Cuando logra vulnerar con éxito a un sistema, esto significará que hay un fallo de seguridad que debera ser solucionado por el equipo correspondiente.  

El funcionamiento de Infection Monkey es sencillo. Está diseñado para enumerar la red, comprobar si hay puertos abiertos y tomar las huellas digitales (fingerprint) de las máquinas, utilizando múltiples protocolos de red. Después de detectar las máquinas vulnerables, intentará atacar a cada una de ellas, utilizando una variedad de métodos que incluyen, entre otros, el bruteforce de contraseñas y la ejecución de exploits conocidos. 

>Infection Monkey es un trabajo en curso y aún queda camino por recorrer para aprovechar al máximo sus ventajas.  

# Componentes de Infection Monkey
Infection Monkey posee dos componentes principales:  

- Monkey Island: un servidor web de [C&C](https://www.trendmicro.com/vinfo/us/security/definition/command-and-control-server) que proporciona una interfaz gráfica para los usuarios e interactúa con los Agentes Monkey.  
- Agente Monkey: un programa binario seguro, similar a un gusano, que explora, propaga y simula técnicas de ataque en **la red local**.  

El operador puede ejecutar el Agente Monkey desde el "Servidor Monkey Island" o distribuir manualmente los binarios del Agente Monkey en la red. Basándose en los parámetros de configuración, los Agentes Monkey escanean, propagan y simulan el comportamiento de un atacante en la red local. Toda la información recopilada sobre la red se agrega en el Servidor Monkey Island y se muestra una vez que todos los Agentes Monkey han terminado.

# Escenario  
El objetivo de este laboratorio es aprender a instalar, configurar y utilizar la herramienta Infection Monkey. Esta herramienta nos permitira realizar ataques a gran escala, empleando ataques conocidos muy efectivos, creando nuestro propio sistema de ataque, todo esto con el fin de mejorar nuestra infraestructura, mejorar el equipo de seguridad y sobre todo mejorar las prácticas utilizadas para la defensa de nuestra organización.  

# Objetivos del laboratorio  
El objetivo de este laboratorio es:  

1 - Aprender a realizar un despliegue exitoso de Infection Monkey  
2 - Aprender a configurar y utilizar Infection Monkey  
3 - Enseñar lo que es posible hacer con Infection Monkey  


# Instalación de Infection Monkey
Vamos a la web oficial de Infection Monkey para solicitar la descargar del programa [Download Infection Monkey]( https://www.guardicore.com/infectionmonkey/). En este sitio nos pide llenar un formulario para que envíen por email el link de descarga del programa.  

![Link_download](img/link1.png)  

Una vez recibido el correo electrónico, procederemos a la descarga del programa. Esta descarga es un archivo en formato [AppImage](https://appimage.org/).  
>Una AppImage es un paquete independiente de la distribución y autoejecutable, que contiene una aplicación y todo lo que puede necesitar para ejecutarse.

El paquete AppImage de Infection Monkey puede ejecutarse en la mayoría de las distribuciones modernas de Linux que tienen FUSE instalado, pero esta certificada por los desarrolladeres, en las siguientes distribuciones y versiones:
- BlackArch 2020.12.01
- Kali 2021.2
- Parrot 4.11
- Rocky 8
- openSUSE Leap 15.3
- Ubuntu Bionic 18.04
- Ubuntu Focal 20.04
- Ubuntu Hirsute 21.04

Mientras de descarga el paquete, procedemos a actualizar nuestro sistema
```
sudo apt update
sudo apt upgrade
```
# Despliegue de Infection Monkey  
Una vez descargado el programa, nos dirigimos al directorio en donde descargamos el programa y una vez alli, ejecutamos lo siguiente:

1 - Hacer ejecutable el paquete AppImage
```
chmod u+x Infection_Monkey_v1.12.0.AppImage
```
2 - Inicie Monkey Island ejecutando el paquete Infection Monkey AppImage
```
./Infection_Monkey_v1.11.0.AppImage  
```
3 - Accede a la interfaz web de Monkey Island. Usando nuestra dirección IP, el protocolo https y el puerto 5000, nos conectaremos a la web de Infection Monkey.  
```
https://<DIRECCION_IP>:5000 
```
1 - Hacemos click en opciones avanzadas.  
2 - Aceptamos el riesgo y continuamos.  

![cert_warning](img/warning.png)  

# Configuración de Infection Monkey  
## Paso 1 - Login por Primera Vez  
La primera vez que iniciamos **Monkey Island** (el servidor de C&C de Infection Monkey), pedirá que se cree una cuenta (login y password). Tras la creación de la cuenta, el servidor solo será accesible a través de las credenciales que se hayan introducido. 
>Para realizar un reset de la cuenta es necesario seguir estos [pasos](https://staging-infectionmonkey.temp312.kinsta.cloud/docs/faq/#resetenable-the-monkey-island-password) 

![login](img/login.png)  

## Paso 2 - Comienzo de Configuración
Una vez iniciada la sesión en Monkey Island procederemos a configurarlo con los parametros del ataque.  

![start](img/start.png)  

## Paso 3 - Configuration Panel (Panel de Configuración)    
En el panel de configuración veremos diferentes pestañas de configuración, pero una de las más importantes es la de los ataques que se van a ejecutar. Pulsando sobre ellas podemos activarlas o desactivarlas.  

![config](img/config.png)  

## Paso 4 - Network Panel (Panel de Red)  
En esta sección se pueden controlar múltiples ajustes importantes, tales como:

- Profundidad de propagación de la red - ¿Cuántos saltos desde la máquina base se propagará Infection Monkey?
- Escaneo de la red local - ¿Debe Infection Monkey intentar atacar cualquier máquina en su subred?
- Lista de IP/subredes del escáner - ¿Qué rangos de IP específicos debe intentar atacar Infection Monkey?

![red](img/red.png)  

## Paso 5 - Guardar siempre los cambios  
Al final de cada sección habrá el siguiente menú en el que está la opción de guardar. Es **MUY** importante siempre guardar los cambios que se realizen en cada pagina.

![save](img/save.png)  

## Paso 6 - Internal Panel (Panel Interno)  
El Internal Panel posee las pestañas para configuraciones mas granulares tanto del servidor Monkey Island, como del Agente Monkey.  
A continuación explicaremos las mas importantes:  

>### Internal Panel/Network  
>Aquí podemos controlar diferentes parametros de la enumeración, como TCP scan interval, TCP scan timeout, Ping scan timeout, ademas de añadir los puertos TCP que queremos >escanear en las máquinas víctimas. Tambien es posible indicar los difererentes numeros de puertos HTTP para realizar enumeración web. 
>
>![ports](img/ports.png)  
>
>### Internal Panel/Monkey  
>Configuramos el número máximo de máquinas que el Agente Monkey puede escanear y tratar de infectar.
>
>### Internal Panel/Island Server
>Lista de C&C/interfaces de red con los que se intentara comunicar el agente Monkey.
>
>### Internal Panel/Exploit
>Configuración de los exploits precargados de Infection Monkey.

## Paso 7 - Exploit Panel (Panel de Explotación)  
En esta sección podemos configurar los diccionarios con usuarios y contraseñas para intentar acceder al sistema que estamos atacando o analizando.

![credentials](img/credentials.png)  

Tambien es esta misma sección se encuentran los "Exploiter", ques son un conjunto de exploits comunes que se pueden utilizar para atacar o analizar los sistemas. Estos exploits son configurables a traves de la pestaña ubicada en "Internal Panel --> Exploits"

![exploit](img/exploit.png)  

# Inicializar Infection Monkey  

Vamos al panel de la izquierda y hacemos click en "Run Mokey" y luego en el panel principal seleccionamos la opción 1, "From Island", esperamos unos minutos y el programa comenzará a ejecutarse.  

![run](img/run.png)  

# Resultados

Una vez que Inicializamos el ataque debemos esperar un tiempo para que se complete, este tiempo dependera de las tareas que hallamos preconfigurado.  


![run-monkey](img/run-monkey.png)  


Cuando Infection Monkey termina el trabajo, nos aparecera el panel de la izquierda de la sigueinte manera:  


![end](img/end.png)


## Paso 1 - Infection Map Panel (Panel del Mapa de Infecciones)  
Aquí veremos un mapa con las máquinas que ha descubierto mediante las técnicas previamente configuradas y nos dará información sobre lo que ha hecho en cada una de ellas pulsando sobre la máquina.  


![map](img/infection_map.png)  


## Paso 2 - Security Reports Panel (Panel de Reportes de Seguridad)  
En esta sección veremos un informe de las máquinas descubiertas, cuales fueron vulneradas y las vulnerabilidades que encontradas.  


![report](img/report.png)  


## Paso 3 - ATT & CK report tab (Reporte ATT&CK)
Este reporte se encuentra en la pestaña dentro del Security Reports Panel. En esta sección podemos ver qué ataques tuvieron éxito y cuáles no.  


![mitre](img/mitre.png)  


## Paso 4 - Logs  
En esta sección podemos ver un detalle de lo que está haciendo Infection Monkey, a qué hora, en qué máquina, el tipo de estrategia y los detalles de lo que se hizo.  


![logs](img/logs.png)  


# Conclusion
Con esta guia se pretende dar a conocer los beneficios de **Infection Monkey** como una herramienta muy poderosa que nos permitira poner a prueba la seguridad en los centros de datos.  

# Mas Información
- [Getting Started whit Infection Monkey](https://www.guardicore.com/infectionmonkey/wt/)  
- [Infection Monkey Official Documentation](https://staging-infectionmonkey.temp312.kinsta.cloud/docs/setup/)  
