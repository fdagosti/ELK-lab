# ELK-lab

## Travaux Pratiques

- Ensure you have Java installed or install it: https://www.oracle.com/technetwork/java/javase/downloads/index.html
  - For windows user, don't forget to [setup the path](https://www.theserverside.com/tutorial/How-to-install-the-JDK-on-Windows-and-setup-JAVA_HOME): Usually it's ``` C:\Program Files\Java\jdk-11.0.1\bin ```
- Download ElasticSearch and unzip it
  - https://www.elastic.co/downloads/elasticsearch
- Download Kibana and unzip it
  - www.elastic.co/guide/en/kibana/current/install.html
  
  
  
## ElasticSearch 

### Exporing cluster
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
```
### Modifying Data
```
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

## Kibana

### Flight Dashboard
#### Filtering
In the Controls visualization, set an Origin City and a Destination City.
Click Apply changes.

The OriginCityName and the DestCityName fields are filtered to match the data you specified.

For example, this dashboard shows the data for flights from London to Newark and Pittsburgh.

To remove the filters, in the Controls visualization, click Clear form, and then Apply changes.
You can also add filters manually. In the filter bar, click Add a Filter and specify the data you want to view.
#### Querying
```
OriginCityName:Rome
OriginCityName:Rome AND (Carrier:JetBeats OR "Kibana Airlines")
```
Kibana has an experimental autocomplete feature that can help jumpstart your queries. To turn on this feature, click Options on the right of the query bar and opt in. With autocomplete enabled, search suggestions are displayed when you start typing your query.
#### Discover
- Index Patterns
- add DestAirportID and DestWeather fields as columns

#### Editing a visualization
- go to dashboard
- click Edit
- Click gear icon on "Average Ticket Price"
- Edit Visualization
- Buckets > Split Group
- Aggregation > Terms
- Field > Carrier
- Descending = 4
- apply change (blue arrow)
- Menubar -> save

### Build your own Dashboard
#### Load sample data
Download the following data structures:
- [The complete works of William Shakespeare](https://download.elastic.co/demos/kibana/gettingstarted/shakespeare_6.0.json).
- [A set of fictitious accounts](https://download.elastic.co/demos/kibana/gettingstarted/accounts.zip) with randomly generated data. 
- [A set of randomly generated log files](https://download.elastic.co/demos/kibana/gettingstarted/logs.jsonl.gz).
```
PUT /shakespeare
{
 "mappings": {
  "doc": {
   "properties": {
    "speaker": {"type": "keyword"},
    "play_name": {"type": "keyword"},
    "line_id": {"type": "integer"},
    "speech_number": {"type": "integer"}
   }
  }
 }
}

PUT /logstash-2015.05.18
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}

PUT /logstash-2015.05.19
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}

PUT /logstash-2015.05.20
{
  "mappings": {
    "log": {
      "properties": {
        "geo": {
          "properties": {
            "coordinates": {
              "type": "geo_point"
            }
          }
        }
      }
    }
  }
}

curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary @accounts.json
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/shakespeare/doc/_bulk?pretty' --data-binary @shakespeare_6.0.json
curl -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/_bulk?pretty' --data-binary @logs.jsonl

GET /_cat/indices?v

```
#### Define index patterns
1. In Kibana, open Management, and then click Index Patterns.
2. If this is your first index pattern, the Create index pattern page opens automatically. Otherwise, click Create index pattern in the upper left.
3. Enter shakes* in the Index pattern field.
4. Click Next step.
5. In Configure settings, click Create index pattern. For this pattern, you don’t need to configure any settings.
6. Define a second index pattern named ba* You don’t need to configure any settings for this pattern.
7. Define an index pattern named logstash*.
8. Click Next step.
9. In Configure settings, select @timestamp in the Time Filter field name dropdown menu.
10. Click Create index pattern.

#### Discovering data
Open Discover.

The current index pattern appears below the filter bar, in this case shakes*. You might need to click New in the menu bar to refresh the data.

Click the caret to the right of the current index pattern, and select ba*.
In the search field, enter the following string:
```
account_number:<100 AND balance:>47500
``` 
#### Visualize data
Open Visualize
Create a visualization
click Pie
New search -> ba* index pattern
n the Buckets pane, click Split Slices.
In the Aggregation dropdown menu, select Range.
In the Field dropdown menu, select balance.
Click Add Range four times to bring the total number of ranges to six.
Define the following ranges:

0             999
1000         2999
3000         6999
7000        14999
15000       30999
31000       50000
Click Apply changes

At the bottom of the Buckets pane, click Add sub-buckets.
In Select buckets type, click Split Slices.
In the Sub Aggregation dropdown, select Terms.
In the Field dropdown, select age.
Click Apply changes

## Beats: ingesting data into kibana
```
curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-6.4.2-darwin-x86_64.tar.gz
tar xzvf metricbeat-6.4.2-darwin-x86_64.tar.gz

./metricbeat modules enable system
./metricbeat setup -e

./metricbeat -e
```

Navigate to the kibana MetricBeat System Overview: http://localhost:5601/app/kibana#/dashboard/Metricbeat-system-overview
