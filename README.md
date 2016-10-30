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

* Creación de excursiones/rutas
* Gestión de inventario/equipamiento
* Consulta sobre excursiones/rutas

Y cada uno de ellos con una Base de Datos.

![Arquitectura microservicios](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/CLOUD%20COMPUTING/Blank%20Diagram%20-%20Page%201%201_zps46fwxuvw.png)

## Back-end

Se utilizará una implementación comentada anteriomente, escrita en Python y se usarán algunas bases de datos, en principio MongoDB

## Front-end

La implementación del bot ya nos proporciona esta parte, así que no debemos preocupernos en principio por la misma.


