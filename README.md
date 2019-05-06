[https://github.com/minsuk-heo/BigData](https://github.com/minsuk-heo/BigData)

[youtube](https://www.youtube.com/playlist?list=PLVNY1HnUlO24LCsgOxR_eK2Yi4sOgH9Pg)

# Elastic Search

ELK 스택의 OS 호환성 여부 표
[https://www.elastic.co/support/matrix](https://www.elastic.co/support/matrix)

Install java
```shell
$ sudo add-apt-repository -y ppa:webupd8team/java
$ sudo apt-get update
$ sudo apt-get -y install oracle-java8-installer
$ java -version
```

Install
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

Start & Stop elastic search
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

# Kibana

Install
```
1. Download DEB file from https://www.elastic.co/downloads/kibana
2. sudo dpkg -i kibana-5.0.2-amd64.deb
```

Config kibana (/etc/kibana/kibana.yml)
```
1. elasticsearch.url: YOUR_ELASTICSEARCH_URL (http://localhost:9200)
2. server.host: the address to which the kibana server will bind (localhost)
```

Start Kibana (/usr/share/kibana/bin/kibana)
```
$ sudo /usr/share/kibana/bin/kibana
```

#### Management    
- `Management - Index Patterns - Index name or pattern`을 본인의 index 이름으로 바꿔준다.

#### Discover
- 처음에는 No results found 라고 뜬다. 우측 상단의 Last 15 minutes를 눌러서 쿼리 대상 시간을 길게 잡아주면 된다.
- 시간 지정 옵션 : quick, relative, absolute
- 바 그래프 밑의 record 들에서 왼쪽 삼각형을 누르면 자세히 보기가 뜬다.
- 자세히 보기 일때 보기 옵션 : table, json
- table일 때 돋보기에 더하기 기호 있는 거 누르면 그 값만 있는 record를 볼 수 있다.
- 쿼리에서 휴지통 버튼 누르면 그 쿼리가 사라진다.
- 원하는 column만 보고 싶으면 record에 창문 처럼 생긴거 누르면 토글이 된다.
- 각 colum의 제목을 누르면 sort가 된다.

#### Visualize
- Vertical Bar Chart 클릭, 원하는 인덱스 클릭
- axis, aggregation, field, custom label, select metrics type 등을 설정하여 그래프 생성
- Pie chart, tile map도 비슷하게

#### Dashboard
- Visualize 탭에서 상단 메뉴 바의 save를 누른다.
- Dashboard 탭에서 상단 메뉴 바에서 Add 버튼을 눌러서 저장해 놓은 차트들을 불러온다.
