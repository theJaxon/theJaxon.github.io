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