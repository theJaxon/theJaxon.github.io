---
title:  "Elastic Certified Engineer"
layout: post
category: elasitcsearch
author: jaxon
tags: ["elasticsearch", "certification"]
---

# Data Management
## Define an index that satisfies a given set of requirements
```json
PUT index
{
  "aliases": {
    "jaxon": {}
  },
  "mappings": {
    "properties": {
      "age": {
        "type": "integer"
      },
      "email": {
        "type": "keyword"
      },
      "name": {
        "type": "text"
      }
    }
  },
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  }
}
```

## Define an use an index template to satisfy a given set of requirements
- Component templates can be re-used in index templates
```json
PUT _component_template/settings
{
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 0
    }
  }
}
```

```json
PUT _index_template/students
{
  "index_patterns": ["students-*"],
  "composed_of": ["settings"]
}
```

## Define and use a dynamic template that satisfies a given set of requirements

```json
PUT dynamic-template-demo
{
  "mappings": {
    "dynamic_templates": [
      {
        "strings_as_ip": {
          "match": "ip-*",
          "match_mapping_type": "string",
          "runtime": {
            "type": "ip"
          }
        }
      },
      {
        "convert-id-to-keyword": {
          "match": "*-id",
          "match_mapping_type": "string",
          "mapping": {
            "type": "keyword"
          }
        }
      }
    ]
  }
}
```

## Define an [index Lifecycle](https://www.elastic.co/guide/en/elasticsearch/reference/current/ilm-index-lifecycle.html#ilm-index-lifecycle) management policy for a timeseries index
- There are **5** index lifecycle phases
1. `Hot` The index is actively being updated and queried.
2. `Warm` The index is no longer being updated but is still being queried.
3. `Cold` The index is no longer being updated and is queried infrequently. The information still needs to be searchable, but it’s okay if those queries are slower.
4. `Frozen` The index is no longer being updated and is queried rarely. The information still needs to be searchable, but it’s okay if those queries are extremely slow.
5. `Delete` The index is no longer needed and can safely be removed.

```json
PUT _ilm/policy/ilm-policy-sample
{
  "policy": {
    "phases": {
      "hot": {
        "min_age": "0ms",
        "actions": {
          "set_priority": {
            "priority": 100
          },
          "rollover": {
            "max_primary_shard_size": "50gb",
            "max_age": "30d"
          }
        }
      },
      "warm": {
        "min_age": "60d",
        "actions": {
          "set_priority": {
            "priority": 50
          }
        }
      },
      "cold": {
        "min_age": "90d",
        "actions": {
          "set_priority": {
            "priority": 0
          }
        }
      }
    }
  }
}

PUT _index_template/data-stream-template
{
  "index_patterns": ["timeseries"],
  # If data_stream isn't specified it will become a regular index template not a data_stream one
  "data_stream": {},
  "template": {
    "settings": {
      "number_of_shards": 1,
      "number_of_replicas": 0,
      "index.lifecycle.name": "ilm-policy-sample"
    }
  }
}
```