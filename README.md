# ELK-lab

## Travaux Pratiques

- Ensure you have Java installed or install it: https://www.oracle.com/technetwork/java/javase/downloads/index.html
  - For windows user, don't forget to [setup the path](https://www.theserverside.com/tutorial/How-to-install-the-JDK-on-Windows-and-setup-JAVA_HOME)
- Download ElasticSearch and unzip it
  - https://www.elastic.co/downloads/elasticsearch
- Download Kibana and unzip it
  - www.elastic.co/guide/en/kibana/current/install.html
  
  
  
## Demo
- ElasticSearch
  - CAT APIs
```
GET /_cat/health?v 

GET /_cat/nodes?v  

GET /_cat/indices?v

PUT /customer?pretty
GET /_cat/indices?v

PUT /customer/_doc/1?pretty
{
"name": "John Doe"
}

GET /customer/_doc/1?pretty

DELETE /customer?pretty
GET /_cat/indices?v

PUT /customer
PUT /customer/_doc/1

GET /customer/_doc/1
DELETE /customer

<HTTP Verb> /<Index>/<Type>/<ID>

PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}

PUT /customer/_doc/1?pretty
{
  "name": "Jane Doe"
}

PUT /customer/_doc/2?pretty
{
  "name": "Jane Doe"
}

POST /customer/_doc?pretty
{
  "name": "Jane Doe"
}

POST /customer/_doc/1/_update?pretty
{
  "doc": { "name": "Jane Doe" }
}

POST /customer/_doc/1/_update?pretty
{
  "doc": { "name": "Jane Doe", "age": 20 }
}

POST /customer/_doc/1/_update?pretty
{
  "script" : "ctx._source.age += 5"
}

DELETE /customer/_doc/2?pretty

POST /customer/_doc/_bulk?pretty
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }

POST /customer/_doc/_bulk?pretty
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}

```
  [Kibana Console](http://localhost:5601/app/kibana#/dev_tools/console?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/_cluster_health/1.json)
  
  [Kibana Console](http://localhost:5601/app/kibana#/dev_tools/console?load_from=https://www.elastic.co/guide/en/elasticsearch/reference/current/snippets/_cluster_health/2.json)
  - explication de l’interface REST
  - ajout de quelques index et data
- Kibana
  - utilisation de l’interface REST via Kibana
  - www.elastic.co/guide/en/kibana/current/getting-started.html
  - Flights Dashboard 
  - Build you own dashboard
    - The complete works of William Shakespeare, suitably parsed into fields. Downloadshakespeare.json.
    - A set of fictitious accounts with randomly generated data. Download accounts.zip.
    - A set of randomly generated log files. Download logs.jsonl.gz.

- Beats: showing the complete stack with dynamic events
  - www.elastic.co/guide/en/beats/metricbeat/6.4/metricbeat-getting-started.html
  - Installation et configuration de metricsBeat
  - Visualisation des métriques systemz
