---
title: Query DSL	
tags: es,Query DSL	
renderNumberedHeading: true
grammar_cjkRuby: true
---

## Query and Filter context

```
Query关注点：此文档与此查询子句的匹配程度如何?
	1）是否包含？
		确定文档是否应该成为结果的一部分
	2）相关度得分多少？
		除了确定文档是否匹配外，查询子句还计算了表示文档与其他文档相匹配的_score
	3）得分越高，相关度越高?
	
Filter关注点：此文档和查询子句匹配么？
	1)是否包含
	2）不涉及评分
	3）针对结构化数据
		适用于完全精确匹配，范围检索
	4)更快
filter查询性能好，可缓存。不许计算得分
query不可缓存；需要计算得分。

Elasticsearch将创建一个文档匹配过滤器的位集bitset（如果文档匹配则为1，否则为0）。 随后用相同的过滤器执行查询将重用此信息。

每当添加或更新新文档时，位集bitset也会更新。

全文检索以及任何使用相关性评分的场景使用query检索。

除此之外的其他使用filter过滤器过滤。

```

## Compound queries

### bool query

多个叶或组合查询语句的默认语句，例如must、should、must_not、filter，must和should子句的分数组合在一起，匹配越多越好，而must_not和filter子句在filter context中执行；

### boosting query

```
返回与正相关匹配的文档，但是减少负相关文档的分数；
```

### constant_score query

```
一个query包装另外的query的查询，但是执行在filter context中。全部匹配的文档都使用相同的"constant"_score
```

### dis_max query

```
一个查询语句接受多个子查询，返回任意一个匹配任意一个查询语句的文档。当bool query合并所有匹配查询的分数时，dis_max查询使用最佳匹配的语句的分数；
```

### function_score query

```
使用函数修改主查询返回的分数，以考虑诸如受欢迎程度，新进度，距离或使用脚本实现的自定义算法等；
```



## 各种查询类型的详细说明

### Boolean query

```
与文档匹配的查询，该文档与其他查询的boolean组合匹配
使用一个或多个boolean子句，每一个子句都有一个类型
```

| occur    | Description                                                  |
| -------- | ------------------------------------------------------------ |
| must     | 查询子句必须出现在匹配的文档中，并且匹配的将有助于得分       |
| filter   | 查询子句必须出现在匹配的文档中。但是并不像must 一样，不会进行评分。filter 的子句被执行在filter context中。意味着评分被忽略，并且查询会被考虑加入缓存； |
| should   | 查询子句应该出现在匹配的文档中                               |
| must_not | 查询子句必须不能出现在匹配的文档中，执行在filter context中，意味着评分被忽略，并且被考虑加入缓存中。由于评分被忽略所以返回的所有文档的评分为0 |

#### Using minimum_should_match

```
使用参数minimum_should_match 指定几个或百分数的should子句必须匹配。
如果一个bool qurey 包含至少一个should子句，没有must和filter，minimum_should_match默认为1，如果有must或filter默认为0；
```

| Type                  | Example     | Description                                                  |
| --------------------- | ----------- | ------------------------------------------------------------ |
| Integer               | 3           | 指定一个固定的值，与可选子句的数量无关；                     |
| Negative   integer    | -2          | 全部的可选子句的数量减去这个值必须被匹配.                    |
| Percentage            | 75%         | 指定可选子句的总数的百分比必须被匹配,这个值的计算是向下取整的. |
| Negative percentage   | -25%        | 指定可选子句总数的百分比可以丢失. 计算同样是向下取整的. 然后从总数减去,以确定这个值 |
| Combination           | 3<90%       | 一个正整数,跟一个<,在跟一个任意的前面提到的判断条件. 表示如果可选子句的数量小于等于这个integer那么需要全部匹配,但是如果大于这个integer的话就会应用和<后面的判断条件 |
| Multiple combinations | 2<-25% 9<-3 |                                                              |

```elasticsearch
这个查询没有指定评分,所以返回的所有文档评分都为0
GET _search
{
  "query": {
    "bool": {
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

```
这个查询因为有一个match_all query,所以会为每个返回的document分配一个1.0的评分
GET _search
{
  "query": {
    "bool": {
      "must": {
        "match_all": {}
      },
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

```
constant_score 与上面的查询的效果一样
GET _search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "status": "active"
        }
      }
    }
  }
}
```

#### Named queries

```
每一个query接受一个_name在顶级中定义.你可以命名queries对于跟踪哪一个query匹配返回的document.如果named queries被使用,响应的hit中会包含match_queries属性

GET /_search
{
  "query": {
    "bool": {
      "should": [
        { "match": { "name.first": { "query": "shay", "_name": "first" } } },
        { "match": { "name.last": { "query": "banon", "_name": "last" } } }
      ],
      "filter": {
        "terms": {
          "name.last": [ "banon", "kimchy" ],
          "_name": "test"
        }
      }
    }
  }
}
```

### Boosting

```
返回与positive查询匹配的document,同时降低与查询匹配的相关性得分 negative.

可以使用boosting查询来降级某些文档,而不必将他们从搜索结果[排除.
GET /_search
{
  "query": {
    "boosting": {
      "positive": {
        "term": {
          "text": "apple"
        }
      },
      "negative": {
        "term": {
          "text": "pie tart fruit crumble tree"
        }
      },
      "negative_boost": 0.5
    }
  }
}

positive
	要运行的查询.返回的所有文档都必须与此查询匹配
negative
	查询用于降低匹配文档的相关性得分.
	如果返回的文档与positive查询和该查询匹配,则该boosting查询将按照如下计算文档的分
	1,从positive查询中获取原始的相关性分数
	2,将分数乘以negative_boost值
	
```

### Constant score query

```
包装一个filter查询,返回匹配的document,每一个document的score等于boost设置的值
GET /_search
{
  "query": {
    "constant_score": {
      "filter": {
        "term": { "user.id": "kimchy" }
      },
      "boost": 1.2
    }
  }
}

constant_score的Top-level参数
filter
boost

```

### Disjunction max query

```
	返回一个与多个字句匹配的文档。
	如果返回的文档与多个查询子句匹配，则该dis_max查询会为该文档分配来自任何匹配子句的最高相关性得分，再加上任何其他匹配子查询的tie breaking打破平局的增量；
	您可以使用dix_max在不同提升因子映射的字段中搜索term
GET /_search
{
  "query": {
    "dis_max": {
      "queries": [
        { "term": { "title": "Quick pets" } },
        { "term": { "body": "Quick pets" } }
      ],
      "tie_breaker": 0.7
    }
  }
}
dis_max的参数
	queries
	(必要的，query数组)包含一个或多个查询子句，返回必须匹配一个或多个语句的document。如果一个document匹配多个查询子句，es使用最高的相关性得分。
   	
    tie_breaker
    (可选的，float)在1-1.0之间的浮点数，用于加上document匹配多个查询子句的相关性得分。
    You can use the tie_breaker value to assign higher relevance scores to documents that contain the same term in multiple fields than documents that contain this term in only the best of those multiple fields, without confusing this with the better case of two different terms in the multiple fields.

	If a document matches multiple clauses, the dis_max query calculates the relevance score for the document as follows:

	1.Take the relevance score from a matching clause with the highest score.
	2.Multiply the score from any other matching clauses by the tie_breaker value.
	3.Add the highest score to the multiplied scores.
	If the tie_breaker value is greater than 0.0, all matching clauses count, but the clause with the highest score counts most.
```

### Function score query

```
function_score可以修改查询到的document的评分，当一个查询的计算是非常昂贵的和。

function_score 可以被使用随着一个function
还支持几个函数一起使用，这种情况下，可以选择仅在文档与给定的filter查询匹配时才能应用function
GET /_search
{
  "query": {
    "function_score": {
      "query": { "match_all": {} },
      "boost": "5", 
      "functions": [
        {
          "filter": { "match": { "test": "bar" } },
          "random_score": {}, 
          "weight": 23
        },
        {
          "filter": { "match": { "test": "cat" } },
          "weight": 42
        }
      ],
      "max_boost": 42,
      "score_mode": "max",
      "boost_mode": "multiply",
      "min_score": 42
    }
  }
}
	Boost for the whole query
	注：每个函数的filtering查询所产生的分数无关紧要

	如果没有给filter指定function则等同于指定match_all
```

