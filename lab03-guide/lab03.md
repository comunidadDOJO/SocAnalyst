![graylog](lab03-images/graylog.png)
---

# Graylog Server en Ubuntu 20.04
## Guia de Instalación
### Sobre Graylog


Graylog es una herramienta de código abierto, utilizada para la agregación y gestión de registros (logs), se utiliza tambien para almacenar, analizar y enviar alertas de los registros recopilados. Con Graylog se pueden analizar registros estructurados y no estructurados utilizando ElasticSearch y MongoDB.  Esto incluye el análisis de registos de una variedad de sistemas operativos (Windows, Linux, Mac, etc.), dispositivos de redes (Firewalls, Switchs, Routers, etc.), dispositivos para [IoT](https://www.graylog.org/post/improving-iot-security-with-log-management), asi como tambien la gestión de registros de diferentes aplicaciones y microservicios.

**Graylog tiene los siguientes componentes**

1. [Servidor Graylog](https://www.graylog.org/products/open-source)
1. [MongoDB](https://www.mongodb.com/es)
1. [ElasticSearch](https://www.elastic.co/es/what-is/elasticsearch)

**Requisitos de hardware**  
La instalación de Greylog requiere de las siguientes caracteristicas mínimas de hardware:

-4 CPU Cores  
-8 GB RAM  
-Ubuntu 20.04 LTS actualizado.  

### Instalación

### Prerequisitos de software  

La instalación de Graylog requiere de una version de Java 8 o superior. Se puede utilizar openJDk 11

```
sudo apt update && apt upgrade -y
```
```
sudo apt install -y apt-transport-https openjdk-11-jre-headless uuid-runtime pwgen curl dirmngr
```
Se puede verificar la version de Java recien instalada utilizando el siguiente comando:

```
java -version
```
```
user@Dojo:~/greylog$ java -version
openjdk version "11.0.11" 2021-04-20
OpenJDK Runtime Environment (build 11.0.11+9-Ubuntu-0ubuntu2.20.04)
OpenJDK 64-Bit Server VM (build 11.0.11+9-Ubuntu-0ubuntu2.20.04, mixed mode, sharing)
```

## Paso 1 – Elasticsearch  

Elasticsearch es un motor de analítica y análisis distribuido, gratuito y abierto para todos los tipos de datos, incluidos textuales, numéricos, geoespaciales, estructurados y no estructurados. Mas Info [aqui](https://www.elastic.co/es/what-is/elasticsearch)  

**Instalación de Elasticsearch**  

Para comenzar la instalación lo primero es descargar la llave de autenticacion GPG del repositorio de elsticksearch e instalarla:  

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
Agregamos el repositorio de elasticsearch a las sources.list de Ubuntu:

```
echo "deb https://artifacts.elastic.co/packages/oss-7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

```
```
sudo apt update
sudo apt install -y elasticsearch-oss
```

Editamos el archivo elasticsearch.yml:  

*NOTA:* Existen muchos editores de texto en Linux, que van desde los mas poderosos como [vim](https://www.youtube.com/watch?v=ggSyF1SVFr4) hasta los mas sencillos como [nano](https://www.youtube.com/watch?v=Jf0ZJZJ8jlI), para efectos de esta guia utilizaremos `nano`  

```
sudo nano /etc/elasticsearch/elasticsearch.yml
```
Buscamos utilizando `ctrl+w` y cambiamos el nombre del cluster a graylog: 

```
cluster.name: graylog
````

Agregamos la siguiente linea al final del archivo:

```
action.auto_create_index: false
```

Despues de terminar de editar el archivo de elasticsearch.yml, guardamos los cambios con `ctrl+x` y confirmamos con `y`  
Luego se inician los servicios de Elasticsearch con los siguientes camandos:

```
sudo systemctl daemon-reload
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```
```
user@Dojo:~/greylog$ sudo systemctl daemon-reload
user@Dojo:~/greylog$ sudo systemctl start elasticsearch
user@Dojo:~/greylog$ sudo systemctl enable elasticsearch
Synchronizing state of elasticsearch.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable elasticsearch
Created symlink /etc/systemd/system/multi-user.target.wants/elasticsearch.service → /lib/systemd/system/elasticsearch.service.
```

Se puede comprobar el estado del servicio de elasticsearch con el siguiente comando:

```
$ systemctl status elasticsearch
```
```
user@Dojo:~/greylog$ systemctl status elasticsearch
● elasticsearch.service - Elasticsearch
     Loaded: loaded (/lib/systemd/system/elasticsearch.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-10-26 23:43:10 -04; 34s ago
       Docs: https://www.elastic.co
   Main PID: 14728 (java)
      Tasks: 40 (limit: 4651)
     Memory: 1.2G
     CGroup: /system.slice/elasticsearch.service
             └─14728 /usr/share/elasticsearch/jdk/bin/java -Xshare:auto -Des.networkaddress.cache.ttl=60 -Des.networkaddress.cache.negative.t>

oct 26 23:42:42 Dojo systemd[1]: Starting Elasticsearch...
oct 26 23:43:10 Dojo systemd[1]: Started Elasticsearch.
```

Elasticsearch se anuncia en el puerto 9200 y se puede verificar con el comando `curl` de la siguiente manera:
```
curl -X GET http://localhost:9200
```

La salida del comando mostrara el nombre del cluster de elasticsearch:
```
user@Dojo:~/greylog$ curl -X GET http://localhost:9200
{
  "name" : "Dojo",
  "cluster_name" : "graylog",
  "cluster_uuid" : "6hGzbcSWTG-NgaJlkxIBCg",
  "version" : {
    "number" : "7.10.2",
    "build_flavor" : "oss",
    "build_type" : "deb",
    "build_hash" : "747e1cc71def077253878a59143c1f785afa92b9",
    "build_date" : "2021-01-13T00:42:12.435326Z",
    "build_snapshot" : false,
    "lucene_version" : "8.7.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```
## Paso 2 – MongoDB  

MongoDB es una base de datos de documentos que ofrece una gran escalabilidad y flexibilidad, y un modelo de consultas e indexación avanzado. Mas Info [aqui](https://www.mongodb.com/es/what-is-mongodb)  

**Instalación de MongoDB**  

MongoDB se descarga y se instala directamente de las fuentes actualizadas de los repositorios de Ubuntu

```
sudo apt update
```
```
sudo apt install -y mongodb-server
```

Luego de finalizada la instalación, se inician los servicios de la siguiente manera:

```
sudo systemctl start mongodb
sudo systemctl enable mongodb
sudo systemctl status mongodb
```
```
user@Dojo:~/greylog$ sudo systemctl start mongodb
user@Dojo:~/greylog$ sudo systemctl enable mongodb
Synchronizing state of mongodb.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable mongodb
user@Dojo:~/greylog$ sudo systemctl status mongodb
● mongodb.service - An object/document-oriented database
     Loaded: loaded (/lib/systemd/system/mongodb.service; enabled; vendor preset: enabled)
     Active: active (running) since Tue 2021-10-26 23:51:28 -04; 1min 53s ago
       Docs: man:mongod(1)
   Main PID: 15671 (mongod)
      Tasks: 23 (limit: 4651)
     Memory: 42.1M
     CGroup: /system.slice/mongodb.service
             └─15671 /usr/bin/mongod --unixSocketPrefix=/run/mongodb --config /etc/mongodb.conf

oct 26 23:51:28 Dojo systemd[1]: Started An object/document-oriented database.
```

## Paso 3 – Graylog

**Instalación de Graylog**

Graylog se descarga desde sus repositorios oficiales de la siguiente manera:

```
wget https://packages.graylog2.org/repo/packages/graylog-4.1-repository_latest.deb
```
Despues de descargar el `.deb`, lo incluimos en nuestro repositorio local: 

```
sudo dpkg -i graylog-4.1-repository_latest.deb
```
Y luego se instala el `.deb` de la siguiente manera:

```
sudo apt update
```
```
sudo apt install -y graylog-server
```
Una vez que instalamos el servidor Greylog, el paso siguiente es configurarlo de manera correcta, para esto necesitamos generar un "secreto" que utilizaremos mas adelante en la configuración de Graylog.  
Este secreto lo podemos generar de la siguiente manera: 

```
pwgen -N 1 -s 96
```
La salida del comando anterior debe ser algo como:

`FFP3LhcsuSTMgfRvOx0JPcpDomJtrxovlSrbfMBG19owc13T8PZbYnH0nxyIfrTb0ANwCfH98uC8LPKFb6ZEAi55CvuZ2Aum`  

Editamos el archivo de configuración de greylog para agregar el "secreto" creado anteriormente:

```
sudo nano /etc/graylog/server/server.conf
```
Buscamos la linea donde este "password_secret = " y agregamos el secreto. 

```
password_secret = FFP3LhcsuSTMgfRvOx0JPcpDomJtrxovlSrbfMBG19owc13T8PZbYnH0nxyIfrTb0ANwCfH98uC8LPKFb6ZEAi55CvuZ2Aum
```

Tambien, en el mismo archivo de configuración, agregamos las siguientes lineas:

```
rest_listen_uri = http://127.0.0.1:9000/api/
web_listen_uri = http://127.0.0.1:9000/
```

El proximo paso es crear el password del administrador de Graylog. Este password se utilizara en la interfaz web para ingresar al sistema.
Hay varias formas de [crear un password que sea lo suficientemete seguro](https://support.google.com/accounts/answer/32040?hl=en), aqui mostramos dos de ellas:   
```
echo -n Str0ng_D0J0_Passw0rd | sha256sum
```
Se debe reemplazar la cadena ‘Str0ng_D0J0_Passw0rd’ con un password de su preferencia pero es recomendable que sea lo suficientemente seguro.

Otra forma alternativa de crear el password es la siguiente:

```
$ echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1

Enter Password: password
```

Obtendremos una salida similar a esta:
```
a8b94ce6251a1529c657fb36e4e405cf772bdd895bb8e887a0696de71f6dbf75
```

Editamos de nuevo el archivo de configuracion de graylog `/etc/graylog/server/server.conf` y colocamos el hash creado en el campo de `root_password_sha2 =`

```
sudo nano /etc/graylog/server/server.conf

root_password_sha2 = a8b94ce6251a1529c657fb36e4e405cf772bdd895bb8e887a0696de71f6dbf75
```

En este mismo archivo agregamos la siguiente linea para que la administración WEB del servidor Graylog, nos responda desde cualquier direccion IP:

```
http_bind_address = 0.0.0.0:9000
```
Luego de finalizar la edicion del archvivo de configuración, inicializamos los servicios de la siguiente manera:

```
sudo systemctl daemon-reload
sudo systemctl restart graylog-server
sudo systemctl enable graylog-server
```
```
user@Dojo:~/greylog$ sudo systemctl daemon-reload
user@Dojo:~/greylog$ sudo systemctl restart graylog-server
user@Dojo:~/greylog$ sudo systemctl enable graylog-server
Synchronizing state of graylog-server.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable graylog-server
Created symlink /etc/systemd/system/multi-user.target.wants/graylog-server.service → /lib/systemd/system/graylog-server.service.
```
Se puede saber si los servicios se inicializaron satisfactoriamente revisando los logs de la siguiente manera:
```
sudo tail -f /var/log/graylog-server/server.log

2021-10-27T20:57:55.985-04:00 INFO  [JerseyService] Started REST API at <0.0.0.0:9000>
2021-10-27T20:57:55.990-04:00 INFO  [ServerBootstrap] Services started, startup times in ms: {JobSchedulerService [RUNNING]=630, PrometheusExporter [RUNNING]=631, OutputSetupService [RUNNING]=636, BufferSynchronizerService [RUNNING]=638, LocalKafkaJournal [RUNNING]=649, LocalKafkaMessageQueueWriter [RUNNING]=651, GracefulShutdownService [RUNNING]=654, UrlWhitelistService [RUNNING]=664, ConfigurationEtagService [RUNNING]=670, EtagService [RUNNING]=680, UserSessionTerminationService [RUNNING]=687, LocalKafkaMessageQueueReader [RUNNING]=697, InputSetupService [RUNNING]=712, MongoDBProcessingStatusRecorderService [RUNNING]=716, LookupTableService [RUNNING]=811, StreamCacheService [RUNNING]=818, PeriodicalsService [RUNNING]=1042, JerseyService [RUNNING]=12078}
2021-10-27T20:57:56.002-04:00 INFO  [ServiceManagerListener] Services are healthy
2021-10-27T20:57:56.023-04:00 INFO  [InputSetupService] Triggering launching persisted inputs, node transitioned from Uninitialized [LB:DEAD] to Running [LB:ALIVE]
2021-10-27T20:57:56.088-04:00 INFO  [ServerBootstrap] Graylog server up and running.
```

si hemos alcanzado este punto, ya tenemos disponible el **Servidor Greylog**, y podremos acceder a traves de la siguiente URL:
```
http://serverip:9000
```
donde, **serverip**, es la dirección IP de la maquina de Ubuntu donde instalamos Greylog.  

![graylog1](lab03-images/graylog1.png)
---
Updated:28/10/2021

**Referencias**
1. https://www.graylog.org/
1. https://computingforgeeks.com/install-graylog-on-ubuntu-with-lets-encrypt/#comments
2. https://docs.graylog.org/docs/ubuntu
