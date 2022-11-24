# Démonstration

## Prérequis

* Docker

## Récupéreration les images docker Elasticsearch et Kibana

```shell
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.5.1
docker pull docker.elastic.co/kibana/kibana:8.5.1
```

## Lancement des conteneurs ES et Kibana

```shell
docker network create elastic
docker run --name es-node01 --net elastic -p 9200:9200 -p 9300:9300 -t docker.elastic.co/elasticsearch/elasticsearch:8.5.1
docker run --name kib-01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.5.1
```

Suivre les instructions pour configurer ElasticSearch et Kibana en local, Elastic fourni un "enrollment token" à saisir dans kibana, ainsi qu'un user/password

> Pour la suite de la démonstration, utiliser la console Kibana : http://0.0.0.0:5601/app/dev_tools#/console
Il est également possible de requêter Elasticsearch directement via HTTP.


## Création d'un index ElasticSearch

```json
PUT /coffee
{
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "price": { "type": "double" },
      "description": { "type": "text" },
      "query" : {
          "type" : "percolator"
        }
    }
  }
}
```

## Indexation de quelques documents

```json
POST /coffee/_doc/
{
  "name": "expresso",
  "price": 1,
  "description": "the short one"
}
POST /coffee/_doc/
{
  "name": "longo",
  "price": 1.2,
  "description": "the long one"
}
POST /coffee/_doc/
{
  "name": "cappucino",
  "price": 1.5,
  "description": "the sweat one"
}
```

## Requêtes pour aller chercher ces documents dans l'index

```json
GET /coffee/_search
{
 "query": {
 "match_all": {}
 }
}

GET /coffee/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 1.3
      }
    }
  }
}
```

## Indexation de requêtes dans l'index

```json
PUT /coffee/_doc/1
{
  "query": {
    "match": {
      "name": "expresso"
    }
  },
  "clientName": "Pierre"
}

PUT /coffee/_doc/2
{
  "query": {
    "match": {
      "name": "hot chocolate"
    }
  },
  "clientName": "Michel"
}

PUT /coffee/_doc/3
{
  "query": {
    "range": {
      "price": {
        "lte": 0.8
      }
    }
  },
  "clientName": "Paul"
}
```

## Execution de Percolate Queries

```json
GET /coffee/_search
{
  "query": {
    "percolate": {
      "field": "query",
      "document": {
        "name": "hot chocolate",
        "price": 2.0,
        "description": "the chocolate one"
      }
    }
  }
}

GET /coffee/_search
{
  "query": {
    "percolate": {
      "field": "query",
      "document": {
        "name": "instant coffee",
        "price": 0.5,
        "description": "the instant one"
      }
    }
  }
}
```


