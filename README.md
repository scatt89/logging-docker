# Logging Docker

An integrated logging and monitoring solution for docker.

## What is Logging-Docker?

Logging-Docker is a Docker-compose project which provides a monitoring and logging solution for your docker container. The following diagram shows the project schema.

![Logging Docker schema](https://github.com/scatt89/logging-docker/blob/master/img/schema.jpg)

As you can see we have a docker apirest container, from now we will call it __app-container__. This container is linked with another container which has a MySQL database. From now we will call this last one __db-container__. Our app-container makes requests to the db-container, which send replies with some data.

For record the _app-container_ and the _db-container_ logs we use a __fluentd-container__. This one stores the logs in a __elasticsearch-container__, which we use because it's great efficiency when indexing and retrieving text data. The _elasticsearch-container_ goes linked with a __kibana-container__, which allows us visualizing in a web interface the stored logs in the _elasticsearch-container_.

## Links
* [Web application repository](https://github.com/scatt89/webApp)
* [Web application container](https://github.com/scatt89/dockerlogviewer)
* [Java api rest container](https://github.com/scatt89/apirest)
* [Fluentd container](https://github.com/scatt89/fluentd)
* [MySQL container](https://hub.docker.com/_/mysql/)
* [Elasticsearch container](https://hub.docker.com/_/elasticsearch/)
* [Kibana container](https://hub.docker.com/_/kibana/)

## How to use this image

### Using Docker-compose
This way is the easiest. You may have docker-compose previously installed in your machine.

Download the docker-compose.yml file from [this](https://github.com/scatt89/logging-docker.git) repository.

* Run _docker-compose_ command in the same docker-compose.yml file directory.

```
$ docker-compose up
```

### Manually run containers
The following console instructions allow us start the Logging-Docker project manually container by container.  

* Run an __elasticsearch-container__

````
$ docker run --name elasticsearch-containner elasticsearch
````

* Run a __kibana-container__ linked to the _elasticsearch-container_

````
$ docker run -p 5601:5601 --name kibana-containner --link elasticsearch-containner:elasticsearch-containner kibana
````

* Run a __fluentd-container__ linked to _elasticsearch-container_

````
$ docker run -p 24224:24224 --name fluentd-containner --link elasticsearch-containner:elasticsearch-containner scatt89/fluentd
````

* Run a __db-container__

````
$ docker run --name db-containner -e MYSQL_ROOT_PASSWORD=myPass -e MYSQL_DATABASE=myDBname mysql
````

* Run an __app-container__ linked to _db-container_

````
$ docker run -p 8080:8080 --name app-containner --link db-containner:db-containner scat89/apirest
````

* Run a __dockerlogviewer-container__

````
$ docker run -d -p 9001:80 --name logvisualizer scatt89/dockerlogviewer
````

* Once finished the previous steps you should be able to see your app-container and db-container logs accessing the following address in your web browser in the Docker Log Viewer app [http://localhost:9001/](http://localhost:9001/) or Kibana [http://localhost:5601](http://localhost:5601)
