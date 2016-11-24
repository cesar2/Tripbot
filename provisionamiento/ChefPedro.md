En primer lugar, he accedido a mi máquina virtual en aws:
```
$ ssh -i "parclaves.pem" ubuntu@ec2-54-243-0-19.compute-1.amazonaws.com
```

![ssh](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/conexionSSH_zpsewsyo7ci.png)


A continuación, he clonado el repositorio de pPedro:
![Clonando repo](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/clonePedro_zpsgmdsqdyf.png)



Ahora, ejecutamos ```sudo chef-solo -c FunnyBot/provisionamiento/Chef/chef/solo.rb```:
![Provisionando](http://i1175.photobucket.com/albums/r629/Cesar_Albusac_Jorge/chef-predo_zpsttj4xmml.png)


Y, como podemos ver en la imagen, ha funcionado correctamente.
