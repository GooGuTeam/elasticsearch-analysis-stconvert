STConvert Analysis for Elasticsearch
==================================

STConvert 是一款中文简繁体转换分析器。
[中文简繁体转换][简体到繁体][繁体到简体][简繁查询展开]

您可以从 [release page](https://github.com/medcl/elasticsearch-analysis-stconvert/releases) 下载预编译包。

插件包括分析器 `stconvert`、
 tokenizer： stconvert`、
 token-filter: `stconvert`、
 和字符过滤器：`stconvert`。

支持的配置：

- `convert_type`: 默认为 `s2t` ,可选选项：
    1. s2t` ,将简体中文字符转换为繁体中文字符
    2. t2s` ,将繁体中文字符转换为简体中文字符

- `keep_both`:default `false` 、

- `delimiter`:default `,`


自定义示例：

```
PUT /stconvert/
{
    "settings" : {
        "analysis" : {
            "analyzer" : {
                "tsconvert" : {
                    "tokenizer" : "tsconvert"
                    }
            },
            "tokenizer" : {
                "tsconvert" : {
                    "type" : "stconvert",
                    "delimiter" : "#",
                    "keep_both" : false,
                    "convert_type" : "t2s"
                }
            },   
             "filter": {
               "tsconvert" : {
                     "type" : "stconvert",
                     "delimiter" : "#",
                     "keep_both" : false,
                     "convert_type" : "t2s"
                 }
             },
            "char_filter" : {
                "tsconvert" : {
                    "type" : "stconvert",
                    "convert_type" : "t2s"
                }
            }
        }
    }
}
```


Analyze tests

```
GET stconvert/_analyze
{
  "tokenizer" : "keyword",
  "filter" : ["lowercase"],
  "char_filter" : ["tsconvert"],
  "text" : "国际國際"
}

Output：
{
  "tokens": [
    {
      "token": "国际国际",
      "start_offset": 0,
      "end_offset": 4,
      "type": "word",
      "position": 0
    }
  ]
}
```

Normalizer usage

```
DELETE index
PUT index
{
  "settings": {
    "analysis": {
      "char_filter": {
        "tsconvert": {
          "type": "stconvert",
          "convert_type": "t2s"
        }
      },
      "normalizer": {
        "my_normalizer": {
          "type": "custom",
          "char_filter": [
            "tsconvert"
          ],
          "filter": [
            "lowercase"
          ]
        }
      }
    }
  },
  "mappings": {
    "properties": {
      "foo": {
        "type": "keyword",
        "normalizer": "my_normalizer"
      }
    }
  }
}

PUT index/_doc/1
{
  "foo": "國際"
}

PUT index/_doc/2
{
  "foo": "国际"
}

GET index/_search
{
  "query": {
    "term": {
      "foo": "国际"
    }
  }
}

GET index/_search
{
  "query": {
    "term": {
      "foo": "國際"
    }
  }
}
```

# Docker 安装方法

```bash
docker exec -it * bin/elasticsearch-plugin install https://github.com/GooGuTeam/elasticsearch-analysis-stconvert/releases/download/v.8.10.3/elasticsearch-analysis-stconvert-8.10.3.zip
```

# 智能中文分析插件

https://www.elastic.co/guide/en/elasticsearch/plugins/8.10/analysis-smartcn.html

'''bash
sudo bin/elasticsearch-plugin install analysis-smartcn
'''
