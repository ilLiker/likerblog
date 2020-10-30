---
title: Search APIS
tabs: es,searchApis
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Search APIS

## Request Body Search

### Query

### From/Size

### Sort

```
可以指定一个或多个字段排序。每一个字段都可以被逆序排序。被定义在字段级别上。特殊的字段_score指定被score排序，_doc指定按照索引顺序排序。

_doc除了是一个有效的排序方式之外，没有任何真实的使用场景，如果你不关心文档的返回顺序，你应该用_doc，这个在scrolling中特别有用。

GET /my_index/_search
{
	"sort": [
		{"post_date" : {"order":"asc"}},
		"user"
		{"name","desc"},
		{"age":"desc"},
		"_score"
	],
	"query":{
		"term" : {"user":"kimchy"}
	}
}

Sort mode option
指定使用哪一种模式排序，可选如下几项
min,max,sum,avg,median,

POST /_search
{
   "query" : {
      "term" : { "product" : "chocolate" }
   },
   "sort" : [
      {"price" : {"order" : "desc", "mode" : "sum"}}
   ]
}

####
Sorting within nested objects
es也支持在一个或多个对象里面对字段进行排序。支持如下选项进行排序：
path
	定义在哪一个对象排序。实际排序的字段必须是嵌套对象的直接字段。当使用嵌套字段排序时，这个字段是必填的。
filter

max_children

nested
```



### Source filtering

```
用来控制怎样处理_source字段返回的结果
1.禁用_source获取 设置为false
GET /_search
{
	"_source" : false,
	"query":{
        "term": {
			"user":"kimchy"
        }
	}
}

2.使用通配符过滤
GET /_search
{
	"_source": "obj.",
	"query": {
		"term": {"user":"kimchy"}
	}
}
OR
GET /_search
{
	"_source":["obj1.*"，"obj2.*"],
	"query": {
		"term": {"user":"kimchy"}
	}	
}
3.指定 includes 和 excludes
GET /_search
{
	"_source" :{
		"includes": ["obj1.*","obj2.*"],
		"encludes": [*.description]
	},
	"query":{
		"term":{
		"user":"kimchy"
		}
	}
}
```



### Search Type

#### Query Then Fetch

```
Parameter value: query_then_fetch
	这个请求处理由两个阶段完成;
	第一个阶段,查询将请求所有相关的shards,每一个shard执行search并产生一个排序的本地shard的结果集。每一个shard返回足够的信息到coordinating node，以使得它能够合并和re-sort这shard level的结果集到一个全局的，最大长度的结果集。
	
	第二阶段，coordination node 只从相关的节点请求document的内容。
```

#### Dfs，Query Then Fetch

```
与Query Then Fetch不同之处在于，在分散阶段就会计算分布项的词频为了更精确的评分。
```





