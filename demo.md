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

```HTTP
PUT /product
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

```HTTP
POST /product/_doc/
{
  "name": "vélo de route",
  "price": 500,
  "description": "suberbe velo de route de la marque xxx"
}
POST /product/_doc/
{
  "name": "gravel",
  "price": 800,
  "description": "magnifique gravel d'occasion"
}
POST /product/_doc/
{
  "name": "vtt",
  "price": 300,
  "description": "extraordinaire VTT, parfait pour rouler dans les petits sentiers"
}
POST /product/_doc/
{
  "name": "roue de vélo",
  "price": 30,
  "description": "une roue de vélo"
}
```

## Requêtes pour aller chercher ces documents dans l'index

```HTTP
GET /product/_search
{
 "query": {
 "match_all": {}
 }
}

GET /product/_search
{
  "query": {
    "range": {
      "price": {
        "lte": 400
      }
    }
  }
}
```

## Indexation de requêtes de percolation dans l'index

```HTTP
PUT /product/_doc/1
{
  "query": {
    "match": {
      "name": "VTC"
    }
  },
  "clientName": "Pierre"
}

PUT /product/_doc/2
{
  "query": {
    "match": {
      "name": "Rustines"
    }
  },
  "clientName": "Michel"
}

PUT /product/_doc/3
{
  "query": {
    "range": {
      "price": {
        "lte": 300
      }
    }
  },
  "clientName": "Paul"
}
```

## Execution de Percolate Queries

```HTTP
GET /product/_search
{
  "query": {
    "percolate": {
      "field": "query",
      "document": {
        "name": "VTC",
        "price": 500,
        "description": "Un super VTC pour alle travailler en ville"
      }
    }
  }
}

GET /product/_search
{
  "query": {
    "percolate": {
      "field": "query",
      "document": {
        "name": "rustines",
        "price": 8,
        "description": "des rustines pour reparer sa roue"
      }
    }
  }
}

GET /product/_search
{
  "query": {
    "percolate": {
      "field": "query",
      "document": {
        "name": "roue de vélo",
        "price": 50,
        "description": "un belle roue de vélo"
      }
    }
  }
}
```


