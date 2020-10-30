---
title: Search API
tabs: es,searchApi
renderNumberedHeading: true
grammar_cjkRuby: true
---

# Search API

## Request Body Search

### Query

```
GET /_search
{
    "query" : {
        "term" : { "user" : "kimchy" }
    }
}
```

### From/Size

```
GET /_search
{
	"from":0,"size":10,
	"query":{
		"term":{"user":"elrei"}
	}
}
Note: form + size 不能大于index.max_result_window 默认10000;
对于深度索引查看Scroll or Search After
```

### Sort

```

```

