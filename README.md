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





