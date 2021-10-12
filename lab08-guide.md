# Detecta el tráfico cifrado malicioso con JA3/JA3S

![Ilustración de Cisco sobre la Detección de Malware en Tráfico Cifrado](https://storage.googleapis.com/blogs-images/ciscoblogs/1/eta-blake-fig-3.png)

## Introducción
El protocolo TLS / SSL permite comunicaciones cifradas, ya sean HTTPS, SSH u otras. El beneficio obvio de este cifrado es que evita que otros puedan ver lo que estamos haciendo. Es por ello, que se se ha convertido en una opción popular en el mundo del malware, ya que los sistemas IDS a menudo no pueden detectar qué está sucediendo. 

Una conexión segura se inicia mediante un [protocolo de enlace de 3 vías](https://programmerclick.com/article/88101040238/) que se produce inmediatamente después del protocolo de enlace de TCP de inicio. Sin embargo, hay una cantidad significativa de información adicional que se transmite en las diversas etapas de este protocolo de enlace (por ejemplo, conjuntos de cifrado, claves o certificados).

JA3 es un método que toma una huella digital de este handshake. Fue publicado por primera vez por  **J**ohn  **A**lthouse,  **J**eff  **A**tkinson y  **J**osh  **A**tkins de Salesforce, de ahí su nombre JA3. Surgió como una solución propuesta para identificar tráfico cifrado malicioso, llegando en un momento dorado, ya que como publicó [Akamai en 2019](https://www.akamai.com/blog/security/bots-tampering-with-tls-to-avoid-detection), ha descubierto que más del 80% del tráfico malicioso actual se realiza a través de canales cifrados.

## Objetivos
Los objetivos que lograrás con la realización de este laboratorio es adquirir conocimientos sobre:
 - Instalación de JA3 en la máquina virtual.
 - Instalación del plugin de JA3 en Wireshark.
 - Instalación de Suricata.
 - Obtención de hashes con Wireshark para la identificación de amenazas.
 - Procesamiento de archivos PCAP con Suricata para la detección de amenazas.

## Recursos a emplear
En este laboratorio utilizará una máquina virtual **Kali Linux 2021** en la que se realizará la instalación de los software:

 - Sistema Operativo: Kali Linux
    - Packet Sniffers: WireShark/TShark, TCPDUMP
    - JA3/JA3S: https://github.com/salesforce/ja3  	
    - WireShark Plugin: https://github.com/fullylegit/ja3
    - Suricata: https://suricata.io/
 - PCAP’s para análisis
    - https://www.malware-traffic-analysis.net/

# Creación del Entorno
En esta sección veremos los pasos para la descarga, instalación y configuración de los programas necesarios para el desarrollo del laboratorio.

## Descarga e Instalación del plugin JA3
### Paso No.1 - Actualicemos los paquetes
Es una buena idea asegurarse de que está ejecutando la última versión. Para ello, abra una terminal y ejecute el siguiente comando para verificar que todo está actualizado: `sudo apt-get update`.

![Actualización de paquetes](https://i.imgur.com/9ACyTZ4.png)

### Paso No.2 - Clonar el repositorio del plugin de Wireshark

Clonar el repositorio de GitHub con el siguiente comando: 
``` 
$ git clone https://github.com/fullylegit/ja3 
```
Para paquetes TLS o SSL, este plugin de Wireshark mostrará información JA3 completa y MD5-hash para paquetes de handshake del cliente. 

![Clonado del repositorio del plugin de Wireshark](https://i.imgur.com/ACz3Z1r.png)

> **Nota**: Puedes verificar que fue clonado a tu computadora ejecutando el comando `ls` y localizando la carpeta con nombre *ja3*.


### Paso No.3 - Crear la carpeta de plugins de Wireshark

Wireshark busca los plugins tanto en una carpeta personal de plugins como en una carpeta global de plugins.  En sistemas tipo Unix, la carpeta personal de plugins es `~/.local/lib/wireshark/plugins`. A continuación la crearemos con el siguiente comando:
```
$ mkdir ~/.local/lib/wireshark/plugins/ -p
```

### Paso No.4 - Copiar el plugin a la carpeta de Wireshark

Para que Wireshark reconozca el plugin que descargamos y pueda *utilizarlo*, ejecutaremos el siguiente comando para copiar el archivo `ja.lua` a la carpeta de plugins de Wireshark:
```
$ cp ja3/ja3.lua ~/.local/lib/wireshark/plugins/
```

## Descarga e Instalación de JA3

### Paso No.1 - Abrir terminal como usuario *root*
La descarga e instalación de JA3 será realizada por el usuario *root*, para abrir una terminal con este usuario, ejecutaremos: 
```
$ sudo bash
```
![Apertura de terminal como usuario root](https://i.imgur.com/tKdzeRX.png)
>**Nota**: Una forma de saber si tenemos una sesión como usuario *root* es ubicando el símbolo al inicio de la línea de comandos:
> - "$"   significa que eres un usuario normal, con privilegios limitados. 
> - "#" significa que usted es el administrador del sistema (root). 

### Paso No.2 - Clonar el repositorio de GitHub de JA3
Moverse al directorio `/opt` antes de clonar el repositorio. Podemos lograr esto con el comando: 
```
# cd /opt
```
Clonar el repositorio de GitHub con el siguiente comando: 
``` 
# git clone https://github.com/salesforce/ja3 
```
![Clonar repositorio del código de JA3](https://i.imgur.com/8pl1Fkt.png)

### Paso No.3 - Instalar pip3
Para instalar paquetes de Python, en este caso las dependencias de JA3, necesitamos contar con *pip* instalado. En caso que no lo esté, lo hacemos con el comando `apt install python3-pip`.

![Instalación de pip3](https://i.imgur.com/GSLHW9g.png)

### Paso No.4 - Instalar las dependencias de JA3
Aun estando en `/opt`, nos desplazaremos con `cd` al directorio de *python* de JA3 ejecutando:
```
# cd ja3/python
```
A continuación, instalaremos las dependencias listadas en el archivo `requirements.txt`
```
# pip3 install -r requirements.txt
```
![Instalación de dependencias de JA3](https://i.imgur.com/lQrhAtw.png)

### Paso No.5 - Ejecutar el *setup* de JA3
Una vez instaladas las dependencias, podemos ejecutar el *setup* de JA3 para realizar su instalación:
```
# python3 setup.py install
```
![Instalación de JA3](https://i.imgur.com/2bxQtfG.png)
Si todo fue ejecutado correctamente, este proceso terminará mostrando el mensaje:  `Finished processing dependencies for pyja3==1.1.0`.

### Paso No.6 - Comprobar la instalación correcta de JA3
Para verificar que JA3 fue instalado exitosamente, ejecutaremos el siguiente comando:
```
# ja3 -h
```
Para el cual obtendremos una salida similar a la siguiente:
```
usage: ja3 [-h] [-a] [-j] pcap

A python script for extracting JA3 fingerprints from PCAP files

positional arguments:
  pcap            The pcap file to process

optional arguments:
  -h, --help      show this help message and exit
  -a, --any_port  Look for client hellos on any port instead of just 443
  -j, --json      Print out as JSON records for downstream parsing
```
>**Nota**: Este es el comando de *help* de JA3, brinda información sobre el uso del programa, incluso más allá de lo contemplado en este laboratorio. 

### Paso No.7 - Instalar Lua-md5
MD5 ofrece facilidades criptográficas básicas para Lua 5.1, las cuales son requeridas en este laboratorio. Instalaremos este paquete con el comando `apt install lua-md5`.

![Instalación del paquete Lua-MD5](https://i.imgur.com/JJ1IY54.png)

### Paso No.8 - Instalar *ncat*
Ncat es una utilidad de red con una funcionalidad similar al comando cat pero para la red. Es una herramienta CLI de propósito general para **leer, escribir y redirigir datos a través de una red**. Está diseñado para ser una herramienta fiable de back-end que se puede utilizar con scripts u otros programas. 

 Instalaremos esta herramienta con el comando `apt install ncat`.

![Instalación de ncat](https://i.imgur.com/HYHxxbv.png)

## Instalación de Suricata

Suricata es un motor de detección de amenazas de red de código abierto que proporciona capacidades que incluyen la **detección de intrusiones** (IDS), la **prevención de intrusiones** (IPS), la **supervisión de la seguridad de la red**.

Funciona muy bien con la inspección profunda de paquetes y la identificación de patrones, lo que lo hace increíblemente útil para la detección de amenazas y ataques.

### Paso No.1 - Instalar Suricata
Para instalar Suricata, el procedimiento es sencillo y directo, tan solo debemos ejecutar el siguiente comando:

```
# apt install suricata -y
```
![Instalación de Suricata](https://i.imgur.com/UzgovOg.png)

### Paso No.2 - Validar la instalación de Suricata
Podemos verificar la instalación correcta de Suricata usando el comando `suricata` en nuestra terminal. Este traerá una salida similar a la siguiente:

![Ejecución del comando *suricata* para verificar la instalación correcta.](https://i.imgur.com/lrTUKhf.png)


# Análisis de tráfico cifrado con JA3 + Wireshark 

Una vez instalados los paquetes y programas, tenemos todo lo necesario para la realización del laboratorio. En esta primera parte, generaremos tráfico cifrado bajo SSL con Netcat y lo guardaremos para visualizar los hashes MD5 a través de Wireshark y JA3.

## Paso No.1 - Ejecutar *tcpdump*
**Tcpdump**  es una herramienta para línea de comandos cuya utilidad principal es analizar el tráfico que circula por la red. En este laboratorio lo utilizaremos para capturar y guardar (en el archivo *SALIDA*, en el directorio `/tmp`) los paquetes transmitidos y recibidos bajo SSL (puerto 443).

Para lograrlo, ejecutaremos en nuestra terminal como *root*:
```
# tcpdump -w /tmp/SALIDA port 443
```
![Ejecución de tcpdump](https://i.imgur.com/5Q5AXSE.png)

## Paso No.2 - Generar tráfico HTTP bajo SSL con *ncat* 
Con *tcpdump* estando en "modo escucha", procederemos a generar tráfico cifrado a través del puerto 443 para que sea capturado y (posteriormente) analizado.

Primero abriremos una nueva terminal como usuario normal. Luego con la herramienta *ncat*, generaremos tráfico HTTP bajo SSL, accediendo a google.com con el siguiente comando:

```
$ ncat --ssl google.com 443
```
![Ejecución del comando ncat --ssl google.com 443](https://i.imgur.com/hCE1Wlm.png)

Seguido de esto, escribiremos lo siguiente (en la terminal) y daremos dos veces a la tecla `ENTER ↵` para hacer una petición al servidor web:
```
GET / HTTP/1.0
```
Esto debe generarnos una salida similar a la siguiente:

![Salida de GET / HTTP/1.0 en google.com](https://i.imgur.com/VG0zb5w.png)

Una vez obtenida esta salida, podemos presionar las teclas `CTRL` +`C` para cortar la conexión.


## Paso No.3 - Repetir el paso No.2 con otro sitio web
Con propósitos comparativos, ejecutaremos nuevamente el comando del paso anterior pero con otro sitio web de su preferencia. Nosotros utilizaremos el sitio `comunidaddojo.org` como ejemplo.

 ```
 ncat --ssl comunidaddojo.org 443
 ```
A diferencia del paso anterior, en lugar de hacer la petición `GET / HTTP/1.0`, haremos la siguiente:

```
HEAD / HTTP/1.0
```
Lo cual nos traerá un resultado similar al siguiente:

![Salida de HEAD / HTTP/1.0 en comunidaddojo.org ](https://i.imgur.com/1YcrLY8.png)

> **Nota**: Recuerda presionar las teclas `CTRL` +`C` para cortar la conexión.

## Paso No.4 - Terminar el proceso de captura de paquetes de *tcpdump*

Una vez que hemos realizado las dos peticiones a distintos sitios web, tendremos suficientes datos para analizar. 

En la terminal *root*, en la que estábamos ejecutando el comando `tcpdump -w /tmp/SALIDA port 443`, presionaremos la combinación de teclas `CTRL` +`C` para finalizar la captura de paquetes.
![Terminación de la ejecución de tcpdump](https://i.imgur.com/EEl0rwk.png)

Una vez hecho esto, *tcpdump* nos indicará la cantidad de paquetes que capturó durante su ejecución.

## Paso No.5 - Abrir el archivo en Wireshark

Wireshark, antes conocido como Ethereal, es un analizador de protocolos utilizado para realizar análisis y solucionar problemas en redes de comunicaciones, para análisis de datos y protocolos, y como una herramienta didáctica.

*Tcpdump* guarda los datos de la captura que realizamos en formato PCAP, que es legible usando Wireshark. A continuación ejecutaremos el siguiente comando, en nuestra terminal de usuario normal, para abrir la captura realizada con Wireshark:
```
$ wireshark /tmp/SALIDA
```
Esto abrirá una ventana de Wireshark similar a la siguiente:

![Ventana de Wireshark recién abierta](https://i.imgur.com/rfzD06s.png)

## Paso No.6 - Ubicar el paquete del 3-way handshake de la primera conexión

Con la ventana maximizada, buscaremos el primer paquete cuya descripción en la columna de *Info* sea "Client Hello" y haremos clic en él.

![Selección del primer paquete de Client Hello](https://i.imgur.com/HbJp7CZ.png)

## Paso No.7 - Copiar el hash generado por JA3
Luego de seleccionar el *Client Hello*, iremos a la sección de "Detalles del paquete" donde abriremos el grupo llamado `ja3/ja3s TLS/SSL fingerprint`. 

![Obtención del ja3 hash de la primera conexión.](https://i.imgur.com/szBxRze.png)

Seguido de esto, observaremos el `ja3 hash` y lo guardaremos en un bloc de notas para compararlo más adelante.

## Paso No.8 - Repetir los pasos No.6 y No.7 para la segunda conexión

Con la misma ventana de Wireshark abierta, buscaremos en la lista de paquetes	el segundo *Client Hello*.
![Selección del segundo paquete Client Hello](https://i.imgur.com/DMrqCfD.png)

Luego, hacemos clic en el grupo `ja3/ja3s TLS/SSL fingerprint` para desplegar más información y visualizar el hash generado por JA3.

![Hash generado por JA3 para la segunda conexión](https://i.imgur.com/WNxN7h8.png)

## Paso No.9 - Comparar los hashes obtenidos
A continuación, observamos ambos hashes:

 - Hash de la conexión a *Google*: `10a6b69a81bac09072a536ce9d35dd43`
 - Hash de la conexión a *ComunidadDojo*: `10a6b69a81bac09072a536ce9d35dd43`

¡Son iguales! ¿Pero por qué? La conexión se realizó hacia distintos servidores web con distintas direcciones IP y, además, en distintos momentos. El hash generado debería ser distinto para las dos conexiones, ¿cierto?

Pues no, los creadores de este método pudieron localizar que algunos de los campos están dictados por bibliotecas y métodos utilizados para construir la aplicación. Como resultado, estos **campos son consistentes entre cada conexión**. 

Específicamente, identificaron que la versión TLS, los conjuntos de cifrado, las extensiones de curvas elípticas, los formatos de puntos de curva elíptica y la longitud de las extensiones podrían usarse para **producir un hash MD5 que se cataloga para **identificar la aplicación****.  

En ambos paquetes *Client Hello*, podemos ver el detalle de los parámetros utilizados para la generación del hash. Son visibles haciendo clic en:
 1. Detalles del paquete
 2. *Transport Layer Security*
 3. *TLSv1.3 Record Layer: Handshake Protocol: Client Hello*
 4. *Handshake Protocol: Client Hello*

 ![Parámetros utilizados para la generación del hash de JA3.](https://i.imgur.com/WTcLxe6.png)

# Obtención del hash a través de la terminal

Tomando en cuenta que no siempre podremos contar con una interfaz gráfica (como la de Wireshark) para obtener el hash generado por JA3, usaremos otro método para visualizarlo.

## Paso No.1 - Ejecutar *JA3* desde la terminal
Abrir una terminal en la que ejecutaremos el siguiente comando:
```
$ ja3 /tmp/SALIDA
```
Lo cual debería mostrar una salida similar a esta:

![Ejecución de ja3 desde la terminal](https://i.imgur.com/NlDsf0I.png)

## Paso No.2 - Analizar archivo *pcap* 
Podemos notar que el *output* del comando contiene un diccionarios de valores por cada conexión realizada, correspondiente a los paquetes *Client Hello*. Es menos información que la mostrada por Wireshark, sin embargo, es información que brinda suficiente contexto sobre la conexión establecida, como:

 - IP (y puerto) de origen
 - IP (y puerto) de destino
 - Hash generado por JA3
 - Timestamp (marca de tiempo)

 # Detección de tráfico cifrado con Suricata

Mientras que algunas soluciones realizan lo que se reduce a un ataque completo de hombre en el medio con el fin de descifrar e inspeccionar el tráfico, puede ser costoso, lento, difícil de implementar, y en realidad podría introducir más problemas de seguridad y privacidad. 

Sin embargo, puede que no sea necesario descifrar el tráfico para marcarlo como malicioso. Por ejemplo, el motor IDPS de **Suricata viene con una integración JA3** que contribuye a supervisar el uso de certificados TLS específicos que pueden combinarse con certificados malos conocidos o con una lista negra de SSL como la que mantiene abuse.ch para alertar o bloquear.

## Paso No.1 - Abrir la configuración principal de Suricata
Abrir una terminal *root*, en ella que nos desplazaremos al directorio que contiene el archivo de configuración de Suricata:
```
# cd /etc/suricata 
```
Seguido de esto, abriremos con nuestro editor de texto preferido el archivo de configuración  *suricata.yaml*. En este caso utilizaremos `nano`:
```
# nano suricata.yaml 
```
![Apertura del archivo de configuración de Suricata](https://i.imgur.com/WYog6Wq.png)

La terminal abrirá *nano* de la siguiente forma:
![Archivo de configuración de Suricata abierto con nano](https://i.imgur.com/FNoYOPV.png)

## Paso No.2 - Editar la configuración principal de Suricata

### Habilitar el uso de *JA3-fingerprints* y *encryption handling*
En el editor de texto `nano`, usaremos la combinación de teclas `CTRL`+`W` para habilitar la búsqueda de texto. Escribiremos `ja3-fingerprints` y daremos a la tecla `ENTER` para buscar.

![Búsqueda del texto ja3-fingerprints en nano](https://i.imgur.com/vV6V4XO.png) 

Esto nos llevará la línea específica que necesitamos modificar:

![Línea ja3-fingerprints a editar ](https://i.imgur.com/ycuvmoh.png)

En esta línea, removeremos el `#` para habilitar la opción, de forma que quede como `ja3-fingerprints: auto`.

Adicional a esto, nos desplazaremos con las flechas del teclado hacia abajo, a la línea que dice `#encryption-handling: default`. Ya ubicados ella, borraremos el `#` y reemplazaremos la palabra `default` por `full`. El resultado final debe ser así:

![enter image description here](https://i.imgur.com/vC043PR.png)

### Modificar el *default-rule-path*
Nuevamente usaremos la combinación de teclas `CTRL`+`W` para realizar búsqueda de texto. Escribiremos `default-rule-path` y daremos a la tecla `ENTER` para buscar.

![Búsqueda de texto default-rule-path](https://i.imgur.com/OqFtfEv.png)

En esta línea, modificaremos la ruta absoluta actual por `/var/lib/suricata/rules/`. 

![enter image description here](https://i.imgur.com/b3VUrhH.png)


### Añadir el archivo de reglas *local.rules*
A continuación, registraremos un archivo de reglas para Suricata. Para ello, nos ubicaremos al final de la línea que dice `- suricata.rules` y daremos a `ENTER` para añadir una nueva línea. 

Presionaremos dos veces la barra espaciadora, para seguir la indentación existente, y escribiremos `- local.rules`. El resultado debe ser el siguiente:

![Resultado del registro del archivo local.rules](https://i.imgur.com/cdOdF41.png)

> **Nota**: Hay un espacio entre el guión y el texto en `- local.rules`.

### Añadir línea de configuración *app-layer*

Estando tan cerca del final del archivo, podemos desplazarnos usando las flechas del teclado. Seguido de esto, añadiremos una nueva línea en que escribiremos `app-layer.protocols.tls.ja3-fingerprints: yes`. Debe quedar así: 

![Nueva línea de configuración añadida](https://i.imgur.com/NszFNoD.png)

### Guardar los cambios realizados
Para guardar los cambios realizados y salir del documento, presionaremos la combinación de teclas `CTRL` + `X`. Esto preguntará si deseamos guardar los cambios, a lo que escribiremos `Y` (por *Yes*) y daremos a `ENTER` para mantener el mismo nombre de archivo.

## Paso No.3 - Crear el archivo de reglas *local.rules*

Para crear el archivo de reglas que registramos en la configuración principal de Suricata, nos desplazaremos a la ruta que especificamos, ejecutando:

```
# cd /var/lib/suricata/rules/
```
Crearemos (y editaremos) el archivo `local.rules` con el comando:
```
# nano local.rules
```
Estando dentro de `nano`, copiaremos la regla para que Suricata sea capaz de detectar el tráfico cifrado de NCAT. La regla es:

```
alert tls any any -> any any (msg:"Deteccion uso NCAT"; ja3.hash; content:"10a6b69a81bac09072a536ce9d35dd43"; reference:url, sitio.com; sid:906300000; rev:1;)
```
![Creación de la regla de detección de de uso NCAT](https://i.imgur.com/QjrlFsu.png)

> **Nota**: El hash empleado en *content* es el mismo que obtuvimos de JA3 en pasos anteriores.

Por último, guardaremos el archivo utilizando la combinación de teclas `CTRL`+`W`. 

## Paso No.4 - Ejecutar *suricata-update*

Habiendo realizado una serie de modificaciones en la configuración de Suricata, es necesario que actualicemos sus fuentes para que los cambios sean ejecutados.

Para esto, ejecutaremos el siguiente comando en nuestra terminal *root*:

```
# suricata-update
```

![Ejecución de suricata-update](https://i.imgur.com/Yy5PMLQ.png)

## Paso No.5 - Ejecutar *suricata* para detectar tráfico mediante NCAT

Una vez Suricata ha sido configurado, podemos probar la regla que creamos si le "damos" la captura realizada previamente para su análisis.

Primero, volveremos al directorio *home*, ejecutando el comando `cd` sin parámetros adicionales.

Luego, haremos que Suricata analice el archivo *pcap* con el comando:

```
# suricata -r /tmp/SALIDA -k none
```
![Ejecución de Suricata para analizar archivo pcap](https://i.imgur.com/4x8fHwx.png)

## Paso No.6 - Revisar archivo *fast.log*

Fast.log es un registro que contiene alertas que consisten en una sola línea. Es en este archivo que deben estar registradas las alertas por el uso tráfico NCAT. Para leerlo, ejecutaremos el siguiente comando:

```
# cat fast.log
```
![Alertas generadas por Suricata](https://i.imgur.com/DSV2XrQ.png)

En efecto, Suricata logró identificar los hashes de JA3 y generó las alertas que habían sido configuradas previamente, una por cada conexión establecida usando NCAT.

# Ejercicios Prácticos
Para validar la comprensión de los pasos realizados, haremos el análisis de dos archivos *pcap*:

 1. Caso No.1: clic [aquí](https://drive.google.com/file/d/1BUKLqxNubhqUIxDxi_Zvz6hCzS-Ep6KT/view?usp=sharing) para descargar
 2. Caso No.2: clic [aquí](https://drive.google.com/file/d/1jvnUrsLQWl4uvlCf-EHCRN5uEjTkb5jU/view?usp=sharing) para descargar

## Enunciado
En estos archivos pcap, se encuentra tráfico cifrado malicioso. Obtén los hashes generados por JA3 e identifica el ataque que se está ejecutando. 

**Consejo**: Busca el hash obtenido en el sitio web: https://sslbl.abuse.ch/ja3-fingerprints/ 

![Captura de pantalla del sitio web SSL Blacklist](https://i.imgur.com/YYQgneS.png)

## Solución
Para la obtención del hash y la identificación del ataque en cuestión, existen (al menos) tres formas de proceder:

 1.  En la terminal de comando, usar la **herramienta *ja3*** para obtener los hashes del archivo pcap. Una vez localizados los hashes, realizar una búsqueda de ellos en el sitio web provisto previamente.  
 2. Usar **Wireshark** para obtener el hash generado por el plugin de JA3 en los "Detalles del paquete" de los *Client Hello*. Una vez localizados los hashes, realizar una búsqueda de ellos en el sitio web provisto previamente.
 3. Descargar el "Conjunto de reglas de *fingerprint* **Suricata** JA3" de [este enlace](https://sslbl.abuse.ch/blacklist/#ja3-fingerprints-suricata). A contiuación, copiar el contenido del archivo en *local.rules* (o registrar un nuevo archivo). Por último, ejecutar Suricata para la detección e identificación de los hashes correspondientes a tráfico cifrado malicioso.

### Demostración del método No.1
A continuación, se presentan los pasos del **método No.1** para la obtención de los hashes e identificación del ataque ejecutado en el **caso No.1.**

#### Paso No.1 - Desplazarnos al directorio del archivo pcap
En nuestro caso, el archivo se encuentra en el directorio `Downloads` del usuario *kali*. Nos movemos a ese directorio utilizando el comando:
```
$ cd Downloads
```
Luego, ejecutamos el comando `ls` para validar que el archivo se encuentra en este directorio.

![Desplazamiento al directorio Downloads](https://i.imgur.com/SftUqCj.png)

#### Paso No.2 - Ejecutar la herramienta ja3
 Para la obtención de los hashes, ejecutaremos la herramienta de Salesforce, `ja3` con el archivo pcap `CASO1.pcap` como parámetro. 

```
$ ja3 CASO1.pcap
```
La salida de este comando debe ser la siguiente: 
![Ejecución del comando ja3 para la obtención de los hashes](https://i.imgur.com/2BD5crB.png)

#### Paso No.3 - Optimizar la obtención de los hashes *(opcional)*
Para este caso práctico en particular, nos encontramos con el mismo hash repetido tres veces. Se darán casos en que la cantidad de hashes a obtener será muy grande. Para ello, sería más eficiente visualizar solamente el *ja3_digest* y los valores únicos (cualquier hash repetido se muestra una sola vez).

Esto se puede lograr *filtrando* la salida con el siguiente comando:

```
$ ja3 CASO1.pcap | grep ja3_digest | sort | uniq
```
En este caso, reducirá la salida a:
![Ejecución del comando ja3 optimizado](https://i.imgur.com/38Uwng3.png)

#### Paso No.4 - Buscar el hash obtenido en SSL Blacklist
A continuación, buscaremos el hash obtenido en *SSL Blacklist* para identificar si corresponde a un ataque registrado.

Hash obtenido: `1d095e68489d3c535297cd8dffb06cb9`
Sitio web: https://sslbl.abuse.ch/ja3-fingerprints/

El resultado de la búsqueda será: 
![Resultado de la búsqueda del hash obtenido](https://i.imgur.com/xEJKY9w.png)

Con este resultado, somos capaces de identificar el ataque como `Tofsee`. Además, podemos obtener información adicional haciendo clic en el valor de la columna *JA3 Fingerprint*.

![Información adicional de Tofsee](https://i.imgur.com/8givbp4.png)

# Conclusión
¡Felicitaciones! Luego de haber completado este laboratorio, eres capaz de realizar:
 - Instalación de JA3 en una máquina virtual.
 - Instalación del plugin de JA3 en Wireshark.
 - Instalación de Suricata.
 - Obtención de hashes con Wireshark para la identificación de amenazas.
 - Procesamiento de archivos PCAP con Suricata para la detección de amenazas.

## Referencias

 - AbuseCh. (s. f.). SSLBL | Detecting malicious SSL connections. SSLBL- AbuseCh. Recuperado 12 de octubre de 2021, de https://sslbl.abuse.ch/ 
 - Althouse, J., Atkinson, J., & Atkins, J. (s.f.). Salesforce/JA3. GitHub. Recuperado 12 de octubre de 2021, de https://github.com/salesforce/ja3 
 - JA3er. (s. f.). JA3er & Displays your JA3 SSL fingerprint. Recuperado 12 de octubre de 2021, de https://ja3er.com
 - Programador Clic. (s. f.). Evite la detección de seguridad aleatorizando TLS. Recuperado 11 de octubre de 2021, de https://programmerclick.com/article/65291044888/ 
 - Ramiro, R. (2020, 3 octubre). Qué son las firmas JA3 / JA3S y cómo nos ayudan en ciberseguridad. Ciberseguridad .blog. https://ciberseguridad.blog/que-son-las-firmas-ja3-ja3s-y-como-nos-ayudan-en-ciberseguridad/
 - Threat Research Team- Akamai. (2019, 15 mayo). Bots Tampering With TLS to Avoid Detection. Akamai. https://www.akamai.com/blog/security/bots-tampering-with-tls-to-avoid-detection

