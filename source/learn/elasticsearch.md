1. install java

2.

groupadd elsearch
useradd elsearch -g elsearch
passwd elsearch
chown -R elsearch:elsearch  elasticsearch-7.6.2


#### 模糊查询


```php

curl -X GET "localhost:22222/bitz/employee/_search?pretty" -H 'Content-Type: application/json' -d'
{
    "query" : {
        "bool": {
            "must": {
                "query_string" : {
                    "fields" : ["last_name"],
                    "query" : "*mit*"
                }
            },
            "filter": {
                "range" : {
                    "age" : { "gt" : 3 } 
                }
            }
        }
    }
}'
```
