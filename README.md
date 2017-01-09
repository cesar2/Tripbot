# Tripbot
Repositorio para el proyecto de la asignatura de Máster Cloud Computing 2016/2017

## Introducción
Este proyecto consiste en el despliegue de una aplicación, en mi caso un Bot de Telegram, realizado con Python.

## Problema

Hoy en día, la abundante información que nos proporciona Internet es sin duda una ventaja para todos. Pero, a veces,
no tenemos una fuente en concreto en la que queremos buscar todo lo que deseamos. Por ejemplo, si queremos buscar 
información sobre rutas, equipamientos o dónde poder subir datos de rutas o excursiones que hayamos realizado, existen 
gran variedad de páginas que nos ofrecen este servicio, pero de manera independiente.

## Solución

La idea consiste en realizar un bot de Telegram a través del cual se podrán subir informaciones sobre rutas o excursiones
que se hayan realizado, o bien consultar tipos de equipamiento necesarios para excursiones existentes o simplemente 
visualizar datos de excursiones (tiempos, lugares, mapas, etc).

# Tecnología

## Arquitectura

Se va a utilizar una arquitectura de microservicios, es decir, estará compuesta por diferentes módulos o servicios que, 
juntos, compondrán la aplicación final, pero a la vez, son independientes unos de los otros.

El Bot que se va a crear utiliza la [Telegram Bot API](https://github.com/eternnoir/pyTelegramBotAPI) que está en Python.

## Microservicios

En un principio se ha planteado la realización de tres microservicios, que pueden actuar independientemente de los demás.

* Creación de excursiones y/o rutas
* Gestión de inventario/equipamiento
* Consulta sobre excursiones/rutas

Y cada uno de ellos con una Base de Datos.

![Arquitectura microservicios](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/CLOUD%20COMPUTING/Blank%20Diagram%20-%20Page%201%201_zps46fwxuvw.png)

## Back-end

Se utilizará una implementación comentada anteriomente, escrita en Python y se usarán algunas bases de datos, en principio MongoDB

## Front-end

La implementación del Bot ya nos proporciona esta parte, así que no debemos preocupernos en principio por la misma.


# Provisionamiento

## Ansible

Como hemos comentado anteriormente, vamos a utilizar Python, ya que se trata de un bot de Telegram y, por lo tanto, lo más razonable es usar ansible, ya que está basado en python.

Antes de comenzar debemos instalar ansible. La manera de hacerlo es la siguiente:
```sudo apt-get install ansible```

Ahora, vamos a crearnos un fichero en el cual especificaremos las herramientas y paquetes que se van a instalar, así como las órdenes que queremos que se ejecuten.
El archivo debe llamarse **playbook.yml** , y el contenido del mismo, en mi caso, es el siguiente:
```
- hosts: all
  sudo: true
  tasks:
    - name: Actualizamos
      apt: update_cache=yes
    - name: Instalar pip
      apt: name=python-setuptools state=present
      apt: name=python-dev state=present 
      apt: name=python-pip state=present
    - name: Instalamos supervisor
      apt: name=supervisor state=present
    - name: Instalamos pyTelegramBotAPI
      pip: name=pyTelegramBotAPI
    - name: Creamos usuario con contraseña y acceso sudo
      user: name=user shell=/bin/bash group=admin password=$6$iTc8Gj93cTvKX$T7l9rxN5ZnphhjxdjI9uYzrkXjfG9qv.ppwvAdPtohRWMn9BtOzhndmDcdiG8EnMVtgnorQrh39yNSdsmwYiS1
```

La contraseña la hemos generado con mkpasswd como se indica en este [enlace](https://www.cyberciti.biz/faq/generating-random-password/). Para instalarlo :
```$ sudo apt-get install makepasswd```

Y para generar la contraseña:
```mkpasswd --method=sha-512```

Lo que nos imprime en consola será lo que pongamos en el campo password del usuario que creamos.

Ahora, debemos crear el fichero ansible_hosts con la información correspondiente a la máquina que vamos a usar:
```
[aws]
ip ansible_ssh_user='ubuntuCC'
```



Por último, si al crear la MV de aws no hemos generado las claves, debemos generar la pareja de claves como sigue:

```aws ec2 create-key-pair --key-name MyKeyPair --query 'KeyMaterial' --output text > MyKeyPair.pem```

Una vez realizados todos los preparativos procedemos a provisionar la máquina:

```sudo ansible-playbook -i ansible_hosts --private-key parclave.pem -b playbook.yml```

![provisionando](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/Captura%20de%20pantalla%20de%202016-11-24%2000-29-57_zpsqenvczqu.png)


Para lanzar la máquina, he seguido los pasos de este [tutorial](https://aws.amazon.com/es/getting-started/tutorials/launch-a-virtual-machine/).


## Chef

Para realizar el provisionamiento con Chef, en primer lugar debemos conectarnos mediante ssh con nuestr máquina en aws. Debemos hacer uso del par de claves privadas que creamos, así
como dar permisos a la clave:
```
$ chmod 400 parclaves.pem
$ ssh -i "parclaves.pem" ubuntu@ec2-54-243-0-19.compute-1.amazonaws.com
```

Ahora, debemos instalar en la máquina tanto git como chef-solo:

```
curl -L https://www.opscode.com/chef/install.sh | sudo bash
sudo apt-get install git

```

Ahora, clonamos el repositorio:
```
$ git clone https://github.com/cesar2/Tripbot.git
```

![clonando](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/clonando_zpshns2bu4c.png)

Y por último provisionamos con chef:
```
sudo chef-solo -c TripBot/provisionamiento/chef/solo.rb
```


![Provisionando con chef](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/chef_zpsbubz6bmb.png)


# Orquestación

He utilizado ansible para realizar la orquestación en Amazon Web Service. 

El archivo que he utilizado es el siguiente: 

```

Vagrant.configure("2") do |config|

config.vm.define "maquina1" do |maquina1|
    maquina1.vm.box = "dummy"

maquina1.vm.provider :aws do |aws, override|
aws.access_key_id = ENV['AWS_KEY']
aws.secret_access_key = ENV['AWS_SECRET']
aws.keypair_name = ENV['AWS_KEYNAME']


aws.tags = {
		'Name' => 'Maquina 1 Vagrant',
		'Environment' => 'vagrant-sandbox'
    } 
    
    aws.region = "us-east-1"
    aws.ami = "ami-b3123fa4"

    aws.instance_type = "t2.micro"
    aws.security_groups = ["launch-wizard-1"]


override.ssh.username = "ubuntu"
override.ssh.private_key_path = ENV['AWS_KEYPATH']
end

maquina1.vm.provision :ansible do |ansible|
ansible.playbook = "playbook.yml"
end
end
    

config.vm.define "maquina2" do |maquina2|
    maquina2.vm.box = "dummy"

maquina2.vm.provider :aws do |aws, override|
aws.access_key_id = ENV['AWS_KEY']
aws.secret_access_key = ENV['AWS_SECRET']
aws.keypair_name = ENV['AWS_KEYNAME']


aws.tags = {
		'Name' => 'Maquina 2 vagrant',
		'Environment' => 'vagrant-sandbox'
    } 
    
    aws.region = "us-east-1"
    aws.ami = "ami-b3123fa4"
    aws.instance_type = "t2.micro"
    aws.security_groups = ["launch-wizard-1"]


override.ssh.username = "ubuntu"
override.ssh.private_key_path = ENV['AWS_KEYPATH']
end


maquina2.vm.provision "chef_solo" do |chef|
    chef.add_recipe "tripbot"

end
end
end

```

Ahora debemos instalar el plugin para utilizar vagrant con aws, tal y como se indica en el siguiente [enlace](http://www.iheavy.com/2014/01/16/how-to-deploy-on-amazon-ec2-with-vagrant/).

![vagrant 1](http://i1155.photobucket.com/albums/p543/cesypozo/buenaProv2_zpsxzrfcvt2.png)

![vagrant 2](http://i1155.photobucket.com/albums/p543/cesypozo/buena_prov_2_2_zps8gscstkz.png)

![vagrant 3](http://i1155.photobucket.com/albums/p543/cesypozo/buena_prov_2_3_zpsj4vg8hul.png)

Tras la ejecución, podemos ver en AWS que las máquinas se han creado correctamente, como se muestra:

![aws mauinas creadas](http://i1155.photobucket.com/albums/p543/cesypozo/2_maquinas_creadas_zpsy91vi0zn.png)


# Contenedores

Para la creación y configuración de entornos de desarrollo virtualizados hemos usado **Docker** , que es capaz de trabajar con múltiples proveedores virtuales.
En primer lugar, debemos de instalarlo, tal y como se indica en el siguiente [enlace](https://docs.docker.com/engine/installation/linux/ubuntulinux/).

También he creado un [repositorio](https://hub.docker.com/r/cesar2/tripbot/) en Docker Hub que se actualiza cada vez que se actualiza nuestro repositorio en GitHub.

Si todo ha funcionado de manera correcta, veremos que en la pestaña Build Details, que el estado es exitoso:

![docker](http://i1155.photobucket.com/albums/p543/cesypozo/Docker2_zpsf4iplesz.png)

El archivo **Docker** es el siguiente:

```
FROM ubuntu:latest

#Autor
MAINTAINER Cesar Albusac Jorge <cesypozo@gmail.com>

#Actualizar Sistema Base
RUN apt-get -y update

#Descargar aplicacion 
RUN apt-get install -y git
RUN git clone https://github.com/cesar2/Tripbot.git


#Instalar Python y mongodb
RUN apt-get -y install mongodb

RUN apt-get install -y python-setuptools
RUN apt-get -y install python-dev
RUN apt-get -y install build-essential
RUN apt-get -y install python-psycopg2
RUN apt-get -y install libpq-dev
RUN easy_install pip
RUN pip install --upgrade pip
```

Puedes ver la comprobación correcta [aquí](https://github.com/cesar2/EjerciciosCloudComputing/blob/master/Temas/comprobandoContenedor.md).


# Inscripción al certamen de Proyectos Libres
![Inscripcion](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/certamen_zpsmtlnx2ze.png)






