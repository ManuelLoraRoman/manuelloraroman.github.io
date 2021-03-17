---
layout: post
title: Rocket.Chat implementado con Docker
date: 2021-03-15 13:31 +0800
last_modified_at: 2021-03-15 13:32:25 +0800
tags: [RocketChat, Docker, Tutorial, LDAP, Ubuntu]
toc:  true
---
Rocket.Chat es una herramienta que permite de manera segura, mantener los datos y las comunicaciones centralizadas.
{: .message }

## Recomendaciones

Antes de empezar a hablar sobre Rocket.chat, voy a explicar un poco el porqué utilizar Rocket.Chat con contenedores.

En principio, los contenedores y las máquinas virtuales difieren en varios aspectos pero el principal
es que los contenedores ofrecen una forma de virtualizar un sistema operativo haciendo que múltiples cargas de
trabajo puedan correr en una sencilla instancia de dicho sistema operativo, mientras que las máquinas virtuales
todo el hardware es virtualizado para correr en diferentes sistemas operativos. 

Esto hace que los contenedores sean más ágiles, veloces y que tengan mayor portabilidad. Además de los siguientes beneficios:

* Requieren menos tiempo para iniciarse

* Mejor distribución de recursos

* Acceso directo al hardware

* Menos redundante

Aparte son más sencillos de orquestar, con diferentes sistemas como Kubernetes, Rancher, Openshift o Docker-compose.

Otra de las recomendaciones es usar un sistema de monitorización, si es posible el estándar Prometheus + Grafana.


## Requerimientos

Los requerimientos han sido ya tratados en dicha sección en el pre-proyecto. Puedes visualizarlo aquí:

https://github.com/ManuelLoraRoman/ApuntesASIR/blob/master/Proyecto.md

Lo único a comentar sería con que voy a trabajar que sería:

* Lenovo Legion Y720

* Debian 10

* 16 GB RAM

* Intel(R) Core(TM) i7-7700HQ CPU @ 2.80GHz

* 8 procesadores con 4 cores cada uno

* 1 TB disco duro

## Instalaciones

Existen varios tipos de instalaciones para Rocket.Chat. Veremos algunos de ellos, y elegiremos cual es el más adecuado.

1. Ubuntu + Snap

{% highlight js linenos %}

sudo apt-get install snapd

sudo snap install rocketchat-server

{% endhighlight %}

Una vez hecho esto, tendríamos desplegado en nuestro _localhost_ Rocket.chat y podremos configurarlo.

Snap actualiza de manera automática cuando hay una nueva version de Rocket.Chat y suele ser una opción más
segura ya que tanto Rocket.Chat y sus dependencias están separadas del sistema.

2. Despliegue en PaaS

Las recomendadas serían _SandStorm_ y _Cloudron_ pero prácticamente se puede desplegar en cualquier entorno,
desde _Amazon Web Services_ hasta en _Digital Ocean_ entre otros.

3. Herramientas de automatización

Es posible instalar el servidor con las siguientes herramientas:

* [Ansible](https://docs.rocket.chat/installation/automation-tools/ansible)

* [OpenShift](https://docs.rocket.chat/installation/automation-tools/openshift)

* [Kubernetes + Helm](https://docs.rocket.chat/installation/automation-tools/helm-chart)

* [Vagrant](https://docs.rocket.chat/installation/automation-tools/vagrant)

4. Instalación manual

Prácticamente, es posible instalar el servidor de Rocket.Chat en diferentes entornos. Algunos de ellos son:

* CentOS

* Debian

* RedHat

* Ubuntu

5. Contenedores Docker

Hemos hablado anteriormente de esta opción.

6. Métodos No-oficiales

Los métodos anteriormente comentados, están respaldados por la Rocket.Chat. Es posible instalar el servidor
en alguno de estos sistemas, pero no se puede asegurar que funcionen o que se actualicen las funcionalidades.
Por comentar algunas, tenemos:

* FreeBSD

* Windows 10 / Windows Server

* Kali

* OpenSUSE


Comentados todos los métodos de instalación, vamos a desarrollar nuestro ejercicio con contenedores Docker. Se ajustan
bien a nuestro escenario y disponen de la portabilidad que buscamos.


## Documentación sobre contenedores Docker y Rocket.Chat

Rocket.Chat dispone de tres imágenes oficiales, siendo la versión _stable_ la que en principio vamos a utilizar. 

Simplemente, para bajarse la imagen, debemos ejecutar lo siguiente:

{% highlight js linenos %}

docker pull rocket.chat

{% endhighlight %}

En caso de querer ejecutar Rocket.Chat sobre _systemd_ vamos a seguir los siguientes pasos:

{% highlight js linenos %}

docker network create rocketchat_default

{% endhighlight %}

Crearemos a continuación dos servicios: el servicio de mongo y el servicio de Rocket.Chat.

* mongo.service

{% highlight js linenos %}

[Unit]
Description=mongo
Requires=docker.service
After=docker.service

[Service]
EnvironmentFile=/etc/environment
User=dockeruser
Restart=always
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill mongo
ExecStartPre=-/usr/bin/docker rm mongo
ExecStartPre=-/usr/bin/docker pull mongo:3.2


ExecStart=/usr/bin/docker run \
    --name mongo \
    -v .../path/to/data/db:/data/db \
    -v .../path/to/data/dump:/data/dump \ <--optional
    --net=rocketchat_default \
    mongo:3.2 \
    mongod --smallfiles --oplogSize 128 --replSet rs0 --storageEngine=mmapv1

ExecStop=-/usr/bin/docker kill mongo
ExecStop=-/usr/bin/docker rm mongo

{% endhighlight %}

* rocketchat.service

{% highlight js linenos %}

[Unit]
Description=rocketchat
Requires=docker.service
Requires=mongo.service
After=docker.service
After=mongo.service

[Service]
EnvironmentFile=/etc/environment
User=dockeruser
Restart=always
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill rocketchat
ExecStartPre=-/usr/bin/docker rm rocketchat
ExecStartPre=-/usr/bin/docker pull rocketchat/rocket.chat:latest

ExecStart=/usr/bin/docker run \
    --name rocketchat \
    -v .../path/to/uploads:/app/uploads \
    -e MONGO_OPLOG_URL=mongodb://mongo:27017/local \
    -e MONGO_URL=mongodb://mongo:27017/rocketchat \
    -e ROOT_URL=https://sub.domain.xx \
    --link mongo:mongo \
    --net=rocketchat_default \
    --expose 3000 \
    rocketchat/rocket.chat:latest

ExecStop=-/usr/bin/docker kill rocketchat
ExecStop=-/usr/bin/docker rm rocketchat

{% endhighlight %}

Una vez creado dichos servicios, iniciamos el servicio de mongo y creamos este contenedor Docker:

{% highlight js linenos %}

docker run \
      --name mongo-init-replica \
      --link mongo:mongo \
      --rm \
      --net=rocketchat_default \
      mongo:3.2 \
      mongo mongo/rocketchat --eval "rs.initiate({ _id: 'rs0', members: [ { _id: 0, host: 'localhost:27017' } ]})"

{% endhighlight %}

E iniciamos el servicio de Rocket.Chat. En caso de tener un _proxy inverso_, nos debemos asegurar que está añadido
en la red creada anteriormente.

## Bibliografía

* www.blackblaze.com/blog/vm-vs-containers/



------
