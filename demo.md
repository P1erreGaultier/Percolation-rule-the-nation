# Démonstration

## Prérequis

* Docker

## Récupérer les images docker ElasticSearch et Kibana

```shell
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.5.1
docker pull docker.elastic.co/kibana/kibana:8.5.1
```

## Récupérer les images docker ElasticSearch et Kibana

```shell
docker network create elastic
docker run --name es-node01 --net elastic -p 9200:9200 -p 9300:9300 -t docker.elastic.co/elasticsearch/elasticsearch:8.5.1
docker run --name kib-01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.5.1
```

> Suivre les instructions pour configurer ElasticSearch et Kibana en local, Elastic fourni un enrollment token à saisir dans kibana, ainsi qu'un user/password
