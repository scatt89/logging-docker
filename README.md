# Logging Docker

Una solución integrada de monitorización y logging para docker.

### Intro
Logging Docker es un proyecto que busca dar una solución de monitorización y logging para tus contenedores docker. En el diagrama siguiente se muestra la estructura de este proyecto.

(diagrama)

Como has podido ver en el diagrama tenemos un contenedor docker con una api-rest, a partir de ahora lo llamaremos app-containner, este contenedor está conectado a otro contenedor que es una base de datos para nuestra aplicación, a partir de ahora lo llamaremos bd-containner. Nuestro app-containner hace peticiones a nuestro bd-containner, este manda respuestas con algunos datos.

Para registrar los logs de app-containner y bd-containner se usa el contenedor fluentd-containner. Este último guarda los logs en un contenedor Elasticsearch, el cual usamos por su gran eficiencia a la hora de almacenar y recuperar texto. El elasticsearch-containner va conectado a un kibana-containner, el cual nos permitirá visualizar en una interfaz web los logs almacenados en elasticsesarch-containner.

### Puesta en marcha

Con las siguientes instrucciones por consola podemos arrancar manualmente todo el proyecto.

1. Arrancamos un contenedor elasticsearch

```sh
$ docker run --name elasticsearch_containner elasticsearch
```
2. Arrancar un contenedor kibana linkado a elasticsearch

```sh
$ docker run -p 5601:5601 --name kebana_containner --link anuncios_elasticsearch:elasticsearch kibana
```
3. Arrancar un contenedor fluentd linkado a elasticsearch
 
```sh
$ docker run -it -p 24224:24224 --name fluentd_containner --link elasticsearch_containner:elasticsearch scatt89/anunciosfluentd:1.0
```

4. Arrancar un contenedor mysql
 
```sh
$ docker run --name db_containner -e MYSQL_ROOT_PASSWORD=myPass -e MYSQL_DATABASE=myDBname -d mysql:latest
```

5. Arrancar un contenedor app linkado a mysql
 
```sh
$ docker run -p 8080:8080 --name app_containner -e MYSQL_CONTAINER=db -e MYSQL_DATABASE= myDBname -e DLL-AUTO=create -e MYSQL-PORT=3306 --link db_containner:db scat89/apiRestApp:latest
```
6. Una vez terminados estos pasos deberías poder ver los logs de tu aplicación y bd accediendo a la siguiente dirección en tu navegador:
http://localhost:5601
