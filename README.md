[https://github.com/minsuk-heo/BigData](https://github.com/minsuk-heo/BigData)

[youtube](https://www.youtube.com/playlist?list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg)

# Elastic Search

ELK 스택의 OS 호환성 여부 표
[https://www.elastic.co/support/matrix](https://www.elastic.co/support/matrix)


```shell
$ sudo add-apt-repository -y ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get -y install oracle-java8-installer
$ java -version
```

```
1. Download DEB file from https://www.elastic.co/downloads/elasticsearch
2. dpkg -i elasticsearch-5.1.1.deb
   Install at : /usr/share/elasticsearch
   Config file at : /etc/elasticsearch
   Init script at : /etc/init.d/elasticsearch
3. sudo systemctl enable elasticsearch.service
   Automatic start and stop
   when server starts and stop
```

```
1. sudo service elasticsearch start
2. sudo service elasticsearch stop
3. curl -XGET 'localhost:9200' # check if elasticsearch run
```

### CRUD of index, type, document

Get information of index
```shell
$ curl -XGET http://localhost:9200/classes # 'classes' is index name
```

Create index
```shell
$ curl -XPUT http://localhost:9200/classes
```

Delete index
```shell
$ curl -XDELETE http://localhost:9200/classes
```

Create document
```shell
$ curl -XPOST http://localhost:9200/classes/class/1/ -d '{"title":"Algorithm", "professor":"John"}'
```

Create document from file
```shell
$ curl -XPOST http://localhost:9200/classes/class/1/ -d @oneclass.json
```

Add one more field
```shell
$ curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"doc":{"unit":"1"}}'
```

Update one field
```shell
$ curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"doc":{"unit":"2"}}'
```

Update one field with script
```shell
$ curl -XPOST http://localhost:9200/classes/class/1/_update -d '{"script":"ctx._source.unit += 5"}'
```

### Bulk

```shell
$ curl -XPOST http://localhost:9200/_bulk --data-binary @classes.json
```

### Mapping

```shell
$ curl -XPUT http://localhost:9200/classes/class/_mapping -d @classesRating_mapping.json
```

### Search

```shell
$ curl -XGET http://localhost:9200/basketball/record/_search
```

Search option - uri
```shell
$ curl -XGET http://localhost:9200/basketball/record/_search?q=points:30
```

Search option - request body [link](https://www.elastic.co/guide/en/elasticsearch/reference/current/search-request-body.html)
```shell
$ curl -XGET http://localhost:9200/basketball/record/_search -d '
{
  "query":{
    "term":{"points":30}
  }
}
'
```

### Aggregation
The aggregations framework helps provide aggregated data based on a search query.    
It is based on simple building blocks called aggregations.    
that can be composed in order to build complex summaries of the data.

structure
```
"aggregations": {
  "<aggregation_name>": {
    "<aggregation_type>": {
      <aggregation_body>
    }
    [,"meta": { [<meta_data_body>] } ]?
    [,"aggregations": { [<sub_aggregation>]+ } ]?
  }
  [,"aggregation_name_2": { ... } ]*
}
```
#### Metric Aggregation
Aggregations that keep track and compute metrics over a set of documents.

Average, Max, Min, Sum, Stats
```
$ curl -XGET localhost:9200/_search --data-binary @avg_points_aggs.json
$ curl -XGET localhost:9200/_search --data-binary @max_points_aggs.json
$ curl -XGET localhost:9200/_search --data-binary @min_points_aggs.json
$ curl -XGET localhost:9200/_search --data-binary @sum_points_aggs.json
$ curl -XGET localhost:9200/_search --data-binary @stats_points_aggs.json
```

#### Bucket Aggregation
Bucket aggregation create buckets of documents. (group by)    
Bucket aggregations can hold sub-aggregations.

Term aggs (group by team)
```shell
$ curl -XGET localhost:9200/_search --data-binary @terms_aggs.json
```

Aggs (stats group by team)
```shell
$ curl -XGET localhost:9200/_search --data-binary @stats_by_team.json
```
