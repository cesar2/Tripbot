## TripBot
#Despliegue

Aquí se ponen en práctica todo lo que hemos visto en la asignatura, realizando un despliege de la infraestructura virtual que utilizará la aplicación **TripBot**.

Como hemos visto anteriormnete, el aplicación se divide en tres microservicios, que se van a ejecutar en 3 máquinas virtuales diferentes. Al comienzo teníamos "Creación de excursiones y/o rutas, Gestión de inventario/equipamiento y Consulta sobre excursiones/rutas. Las he simplificado en dos, que sería excursiones y rutas e Inventario y equipamiento.
Aun así, seguiríamos teniendo 3 servicios: El Bot de Telegram, un servicio de Excursiones y rutas y un último servicio con información de Inventario y equipamiento.
Se va a utilizar Amazon Web Services (AWS),ya que es la que hemos estado utilizando anteriormente.


# Gestión de Excursiones y rutas

Como vimos al principio del proyecto, la idea era utilizar una Base de Datos MongoDB. Por ello, la manera más sencilla para gestionar la misma es utilizar un servicio externo.
Vamos  a usar **[mLab]**(https://mlab.com/), que nos orfrece de manera gratuita hasta 500MB de almacenamiento de datos. Solamente tenemos que crearnos una cuenta y crear la/s BBDD/s que deseemos.

![mlab](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/ultimo%20hito/mongo_zpslpxxevzd.png)

Ahora tenemos que utilizar la URI que nos proporcionan para conectarnos a la misma. Para utilizarla hay que seguir los pasos de este [enlace](). 
Debemos tener la librería **mlban** en python. El provisionamiento para este microservicio debe ser modificado para añadir dicha libreria.


# Gestión de inventario y equipamiento.

Volvemos a necesitar una base de datos MongoDB, por lo que debemos repetir los pasos anteriores.


# Telegram Bot 

Este microservicio es el que alberga la funcionalidad principal sin la cuál los demás servicios quedarían inutilizados. Ésta interacciona con el usuario, por lo que vamos a configurar un servicio de log en el mismo. Vamos a utilizar un servicio externo para ello. En concreto, vamos a utilizar papertrail.

Su funcionamiento es muy sencillo, basta con registrarse, seleccionar el sistema que estamos usando y configurar nuestra aplicación, como se indica en el siguiente [enlace](http://help.papertrailapp.com/kb/configuration/configuring-centralized-logging-from-python-apps/)

![log](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/ultimo%20hito/8223b115-8cbf-41c9-b324-0b69384fab43_zpsv41cdd3i.png)


Las librerias necesarias para el uso del sistema de log, logging y socket, vienen instaladas por defecto en python, por lo que no es necesario instalarlas

Para cada uno de dichos servicios se ha creado su correspondiente contenedor docker:

* [Contenedor en el que se encuentra el Bot de Telegram](https://hub.docker.com/r/cesar2/tripbot/)(realizado anteriormente) 
* [Contenedor Excursiones y rutas](https://hub.docker.com/r/cesar2/tripbot-rutas_y_excursiones/)
* [Contenedor Inventario y equipamiento](https://hub.docker.com/r/cesar2/tripbot-equipamiento/)

Estos contenedores son los que se desplegarán en AWS utilizando Vagrant.

Ahora que sabemos el funcionamiento de los distintos microservicios y sus contenedores asociados generamos los playbook necesarios y el correspondiente Vagrantfile.


Finalmente usamos vagrant up --provider=aws para desplegar todas las máquinas.


