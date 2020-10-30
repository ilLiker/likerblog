---
Title: es聚合
---

## Bucket

```
分桶类型，类似SQL中的group by 语法
GET /my_index/_search
{
  "aggs": {
    "type_sum_age": { # 聚合后的名称
      "terms": {      # 聚合类型
        "field": "type.keyword"
      }
    }
  }
}
如上例子，查询是根据type来进行分桶，类似 SQL中的GROUP BY type
			之所以使用type.keyword是因为聚合操作不支持text字段
如下为查询结果：
{
  "took" : 1,
  "timed_out" : false,
  "_shards" : {
    "total" : 1,
    "successful" : 1,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : {
      "value" : 28,
      "relation" : "eq"
    },
    "max_score" : 1.0,
    "hits" : [
      。。。
    ]
  },
  "aggregations" : {
    "type_sum_age" : {
      "doc_count_error_upper_bound" : 0,
      "sum_other_doc_count" : 0,
      "buckets" : [
        {
          "key" : "lion",
          "doc_count" : 7
        },
        {
          "key" : "cat",
          "doc_count" : 6
        },
        {
          "key" : "dog",
          "doc_count" : 6
        },
        {
          "key" : "mouse",
          "doc_count" : 6
        },
        {
          "key" : "tigger",
          "doc_count" : 3
        }
      ]
    }
  }
}
```

## Metric

```
指标分析类型，如计算最小值、最大值、平均值等等
```

### 单值分析

```
指输出一个分析结果
- min,max,avg,sum
- cardinality
```

#### min

```
# 返回数值类字段的平均值
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "age_avg": {
      "avg": {
        "field": "age"
      }
    }
  }
}
# max,min,sum 不做赘述
```

#### cardinality

```
# cardinality，意为集合的势，或者基数，是指不同的数值的个数，类似SQL中的distinct count的概念
GET /my_index/_search
{
  "size": 0, 
  "aggs": {
    "count_of_type": {
      "cardinality": {
        "field": "type.keyword"
      }
    }
  }
}
```

### 多值分析

 ```
输出多个结果
- stats,extended stats
- percentile,percentile rank
- top hits
 ```

#### stats/extended stats

```

```



## Pipeline

```
管道分析类型，基于上一级的聚合分析结果进行再分析
```

## Matrix

```
矩阵分析类型

```

# 测试数据

```

PUT /_bulk
{ "index" : { "_index" : "my_index", "_id" : "1" } }
{  "type":"dog", "age":14,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "2" } }
{ "type":"dog","age":1,"job": "bike"}
{ "index" : { "_index" : "my_index", "_id" : "3" } }
{ "type":"cat",  "age":3,  "job": "car"}
{ "index" : {"_id" : "22", "_index" : "my_index"} }
{ "type":"lion", "age":24, "job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "4" } }
{  "type":"cat", "age":23,"job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "5" } }
{ "type":"mouse","age":18,"job": "bike"}
{ "index" : { "_index" : "my_index", "_id" : "6" } }
{ "type":"tigger",  "age":15,  "job": "car"}
{ "index" : {"_id" : "23", "_index" : "my_index"} }
{ "type":"mouse", "age":14, "job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "7" } }
{  "type":"lion", "age":12,"job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "8" } }
{ "type":"mouse","age":11,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "9" } }
{ "type":"dog",  "age":8,  "job": "car"}
{ "index" : {"_id" : "24", "_index" : "my_index"} }
{ "type":"dog", "age":9, "job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "10" } }
{  "type":"lion", "age":6,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "11" } }
{ "type":"cat","age":10,"job": "bike"}
{ "index" : { "_index" : "my_index", "_id" : "12" } }
{ "type":"cat",  "age":5,  "job": "car"}
{ "index" : {"_id" : "25", "_index" : "my_index"} }
{ "type":"lion", "age":7, "job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "13" } }
{  "type":"lion", "age":19,"job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "14" } }
{ "type":"cat","age":30,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "15" } }
{ "type":"cat",  "age":20,  "job": "car"}
{ "index" : {"_id" : "26", "_index" : "my_index"} }
{ "type":"lion", "age":17, "job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "16" } }
{  "type":"dog", "age":18,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "17" } }
{ "type":"mouse","age":15,"job": "bike"}
{ "index" : { "_index" : "my_index", "_id" : "18" } }
{ "type":"tigger",  "age":14,  "job": "car"}
{ "index" : {"_id" : "27", "_index" : "my_index"} }
{ "type":"dog", "age":12, "job": "bus"}
{ "index" : { "_index" : "my_index", "_id" : "19" } }
{  "type":"lion", "age":21,"job": "car"}
{ "index" : { "_index" : "my_index", "_id" : "20" } }
{ "type":"mouse","age":14,"job": "bike"}
{ "index" : { "_index" : "my_index", "_id" : "21" } }
{ "type":"tigger",  "age":16,  "job": "car"}
{ "index" : {"_id" : "28", "_index" : "my_index"} }
{ "type":"mouse", "age":13, "job": "car"}

GET /my_index/_mapping

DELETE /my_index

PUT /my_index/
{
  "mappings": {
    "properties": {
      "type": {
        "type":"text",
        "fields": {
          "keyword":{
            "type":"keyword"
          }
        }
      },
      "age":{
        "type": "integer"
      },
      "job": {
        "type": "text",
        "fields": {
          "keyword":{
            "type":"keyword"
          }
        }
      }
    }
  }
}
```

