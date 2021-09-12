Instalar Graylog Server on Ubuntu 20.04

<h1><span style="color:blue">Introducción</span></h1>


Graylog es una herramienta de agregación y gestión de registros de código abierto que puede utilizarse para almacenar, analizar y enviar alertas de los registros recopilados. Graylog puede utilizarse para analizar registros estructurados y no estructurados utilizando ElasticSearch y MongoDB.  Esto incluye una variedad de sistemas, incluyendo sistemas Windows, sistemas Linux, diferentes aplicaciones y microservicios, etc.

**Graylog tiene los siguientes componentes**

1. Servidor Graylog
1. MongoDB
1. ElasticSearch

<h1><span style="color:blue">Requisitos</span></h1>

4 CPU Cores
8 GB RAM
Ubuntu 20.04 LTS installed and updated.

<h1><span style="color:blue">Inicia la instalación</span></h1>

<span style="color:red">Step 1 – Instalar Java en Ubuntu 20.04</span>

Java version 8 and above is required for Graylog installation. In this post, we shall use open JDK 11

```
sudo apt update && apt upgrade -y
```
```
sudo apt install -y apt-transport-https openjdk-11-jre-headless uuid-runtime pwgen curl dirmngr
```

You can verify the java version you just installed using the java -version command:

`````
 java -version
`````
`````
 java -version
`````

<span style="color:red">Step 2 – Install Elasticsearch on Ubuntu 20.04</span>


```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
```
Add Elasticsearch repository to your sources list:

```
echo "deb https://artifacts.elastic.co/packages/oss-7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list

```
**Instalar Elasticsearch**
```
sudo apt update
sudo apt install -y elasticsearch-oss
```

Configure cluster name for Graylog.
```
sudo vim /etc/elasticsearch/elasticsearch.yml
```
Edit the cluster name to graylog
```
cluster.name: graylog
````
Add the following information in the same file

```
action.auto_create_index: false
```
Reload daemon the start Elasticsearch service.
```
sudo systemctl daemon-reload
sudo systemctl start elasticsearch
sudo systemctl enable elasticsearch
```

You can check for the status of the service by :
```
$ systemctl status elasticsearch
```

Elasticsearch runs on port 9200 and this can be virified by curl command:
```
curl -X GET http://localhost:9200
```
You should see your cluster name in the output.


***Step 3 – Install MongoDB on Ubuntu 20.04
Download and install mongoDB from Ubuntu’s base repository.***
```
sudo apt update
```
```
sudo apt install -y mongodb-server
```
```
Start MongoDB
```
```
sudo systemctl start mongodb
```
```
sudo systemctl enable mongodb
```
```
sudo systemctl status mongodb
```
***Step 4 – Install Graylog Server on Ubuntu 20.04
Download and configure Graylog repository.***
```
wget https://packages.graylog2.org/repo/packages/graylog-4.1-repository_latest.deb
```
```
sudo dpkg -i graylog-4.1-repository_latest.deb
```
**Install Graylog server:**
```
sudo apt update
```
```
sudo apt install -y graylog-server
```
Generate a secret to secure user passwords using pwgen command

```
pwgen -N 1 -s 96
```
The output should look like:

FFP3LhcsuSTMgfRvOx0JPcpDomJtrxovlSrbfMBG19owc13T8PZbYnH0nxyIfrTb0ANwCfH98uC8LPKFb6ZEAi55CvuZ2Aum
Edit the graylog config file to add the secret we just created:
```
sudo vim /etc/graylog/server/server.conf
```
Locate the password_secret = line and add the above created secret after it.

password_secret= FFP3LhcsuSTMgfRvOx0JPcpDomJtrxovlSrbfMBG19owc13T8PZbYnH0nxyIfrTb0ANwCfH98uC8LPKFb6ZEAi55CvuZ2Aum
Also add the following lines to the /etc/graylog/server/server.conf file
```
rest_listen_uri = http://127.0.0.1:9000/api/
web_listen_uri = http://127.0.0.1:9000/
```
The next step is to create a hash sha256 pasword for the administrator. This is the password you will need to login to the web interface.

```
echo -n Str0ngPassw0rd | sha256sum
```
Replace ‘Str0ngPassw0rd’ with a password of your choice.

Alternatively use the command below:

```$ echo -n "Enter Password: " && head -1 </dev/stdin | tr -d '\n' | sha256sum | cut -d" " -f1
```
Enter Password: password
You will get an output of this kind:

e3c652f0ba0b4801205814f8b6bc49672c4c74e25b497770bb89b22cdeb4e951
Edit the /etc/graylog/server/server.conf file then place the hash password at root_password_sha2 =

```
sudo vi /etc/graylog/server/server.conf
```
root_password_sha2 = e3c652f0ba0b4801205814f8b6bc49672c4c74e25b497770bb89b22cdeb4e951
Graylog is now configured and ready for use.

**Start Graylog service:**
```
sudo systemctl daemon-reload
sudo systemctl restart graylog-server
sudo systemctl enable graylog-server
```
You can check if the service has started successfully from the logs:

```
sudo tail -f /var/log/graylog-server/server.log
```


Graylog server up and running.
If you would like to Graylog interface with Server IP Address and port, then set http_bind_address to the public host name or a public IP address of the machine you can connect to

```
http_bind_address = 0.0.0.0:9000
```
And restart graylog-server service

```
sudo systemctl restart graylog-server
```
You can then access graylog web dashboard on:

```
http://serverip_hostname:9000
```
Updated:12/09/2021

Referencias
1. https://www.graylog.org/

1. https://computingforgeeks.com/install-graylog-on-ubuntu-with-lets-encrypt/#comments