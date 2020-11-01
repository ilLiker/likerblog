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

### 常见的分桶策略

#### Terms

```
该分桶策略最简单，直接按照term来分桶，如果是text类型，则按照分次后的结果分桶
```

#### Range

````
通过指定数值的范围来设定分桶规则
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "age_range": {
      "range": {
        "field": "age",
        "ranges": [
          {
            "to": 10
          },{
            "from": 10,
            "to": 20
          },{
            "from": 20
          }
        ]
      }
    }
  }
}
````

#### Date Range

```
通过指定日期的范围来设定分桶规则
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "date_range": {
      "range": {
        "field": "birth",
        "format":"yyyy",
        "ranges": [
          {
            "from": "1980",
            "to": "1990"
          },{
            "from": "1990",
            "to":"2000"
          },
          {
            "from": "2000"
          }
        ]
      }
    }
  }
}
```

#### Histogram

```
直方图，以固定的间隔策略来分隔数据
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "salary_hits": {
      "histogram": {
        "field": "age",
        "interval": 5,
        "extended_bounds": {
          "min": 0,
          "max": 50
        }
      }
    }
  }
}
```

#### Date Histogram

```
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "by_year": {
      "date_histogram": {
        "field": "brith",
        "interval": "year",
        "format": "yyyy"
      }
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
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "stats_age": {
      "extended_stats": {
        "field": "age"
      }
    }
  }
}

GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "stats_age": {
      "stats": {
        "field": "age"
      }
    }
  }
}
```

#### percentile,percentile rank

```
#百分位数统计
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "per_age": {
      "percentiles": {
        "field": "age",
        "percents": [
          1,
          5,
          25,
          50,
          75,
          95,
          99
        ]
      }
    }
  }
}
# 百分位数统计
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "per_salary": {
      "percentile_ranks": {
        "field": "age",
        "values": [
          10,
          15
        ]
      }
    }
  }
}
```

#### Top Hits

```
一般用于分桶后获取该桶内最匹配的顶部文档列表，即详情数据

GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "jobs": {
      "terms": {
        "field": "type.keyword",
        "size": 10
      },
      "aggs": {
        "top_ages": {
          "top_hits": {
            "size": 10,
            "sort": [
              {
                "age": {
                  "order": "desc"
                }
              }
            ]
          }
        }
      }
    }
  }
}
```

## Pipeline

```
管道分析类型，基于上一级的聚合分析结果进行再分析

针对聚合分析的结果再次进行聚合分析，而且支持链式调用，可以回答如下问题：
	订单月平均销售额是多少？
POST /my_index/_search
{
  "size": 0,
  "aggs": {
    "sales_per_month": {
      "date_histogram": {
        "field": "date",
        "interval": "month"
      },
      "aggs": {
        "sales": {
            "sum": {
              "field": "price"
            }
        }
      }
    },
    "avg_monthly_sales":{
      "avg_bucket": {
        "buckets_path": "sales_per_month>sales"
      }
    }
  }
}
	
```

### Pipeline的分析结果会输出到原结果中，根据输出位置的不同，分为一下两类：

#### Parent

```
结果内嵌到现有的聚合分析结果中
Derivative
Moving Average
Cumulative Sum
```

#### Sibling

```
结果与现有聚合分析结果同级
Max/Min/Avg/Sum Bucket
Stats/Extended Stats Bucket
Percentiles Bucket
```



#### 

## Matrix

```
矩阵分析类型

```

### Bucket + Metric

```
Bucket聚合分析允许通过添加子分析来进一步进行分析，该子分析可以是Bucket也可以是Metric。
分桶后再分桶

GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "types": {
      "terms": {
        "field": "type.keyword",
        "size": 10
      },
      "aggs": {
        "age_range": {
          "range": {
            "field": "age",
            "ranges": [
              {
                "from": 0,
                "to": 10
              },{
                "from": 10,
                "to": 20
              },{
                "from": 20,
                "to": 30
              }
            ]
          }
        }
      }
    }
  }
}

#分桶后数据分析
GET /my_index/_search
{
  "size": 0,
  "aggs": {
    "types": {
      "terms": {
        "field": "type.keyword",
        "size": 10
      },
      "aggs": {
        "type_stats": {
          "stats": {
            "field": "age"
          }
        }
      }
    }
  }
}
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

