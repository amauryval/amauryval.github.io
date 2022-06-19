---
title: "Snippets - Json / YAML & JsonSchema"
last_modified_at: 2020-12-06T11:37:45
categories:
  - Snippets
tags:
  - Json
  - JsonSchema
  - YAML
  - Python
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---

<figure style="width: 200px" class="">
  <a href="/assets/images/memes/json.jpg"><img src="/assets/images/memes/json.jpg"></a>
</figure>


Json, YAML (as XML) are exchange formats which can be used to store data (GeoJSON for example)
It's a good practice to use them as input parameter of a process, tools. It's possible to validate your input data by defining a Json Schema.
So Json Schema can be used as a pattern to validate the data defined in your Json or Yaml formats.

They are more easy to use than XML because readability is better

Documentation : 
* [tutorial](https://deusyss.developpez.com/tutoriels/Python/json_schema/)
* [Official documentation !recommanded](http://json-schema.org/understanding-json-schema/)

Tools to help you
* [Json validator](https://jsonformatter.curiousconcept.com/)
* [Json2Yaml](https://jsonformatter.org/json-to-yaml)
* [Json & JsonSchema validator](https://www.jsonschemavalidator.net/)
* [Json editor](https://jsoneditoronline.org/beta/#left=local.leduze)

# Json Schema with Python

## From YAML

An example :
```yaml
first_name: John
age: 42
alive: true
children:
  - first_name: chuck
    alive: true
```

```python
import yaml
import json
from jsonschema import validate

# load YAML input data
input_json_data = yaml.full_load(input_yaml_text_data)  # return a dict

# load Json Schema input data
input_json_schema_data = json.loads(input_json_schema_text_data)

# validate input data with its schema
validate(instance=input_data, schema=input_schema_data)
# An exception is returned if data and schema don't match
```

## From Json

An example :
```json
{
    "first_name": "John",
    "age": 42,
    "alive": true,
    "children": [
        {
            "first_name": "chuck",
            "alive": true
        }
    ]
}
```

```python
import json
from jsonschema import validate

# load Json input data
input_json_data = json.loads(input_json_text_data)  # return a dict

# load Json Schema input data
input_json_schema_data = json.loads(input_json_schema_text_data)

# validate input data with its schema
validate(instance=input_data, schema=input_schema_data)
# An exception is returned if data and schema don't match
```

## Json Schema 

Example:
```
{
    "$schema": "http://json-schema.org/draft-07/schema",
    "type": "object",
    "required": [
        "first_name",
        "age",
        "alive",
        "children"
    ],
    "properties": {
        "first_name": {
            "type": "string"
        },
        "age": {
            "type": "integer",
            "default": 0
        },
        "alive": {
            "type": "boolean",
            "default": false
        },
        "children": {
            "type": "array",
            "default": [],
            "additionalItems": true,
            "items": {
                "type": "object",
                "required": [
                    "first_name",
                    "alive"
                ],
                "properties": {
                    "first_name": {
                        "type": "string",
                        "default": ""
                    },
                    "alive": {
                        "type": "boolean",
                        "default": true
                    }
                },
                "additionalProperties": false
            }
        }
    },
    "additionalProperties": true
}
```

## Snippet(s)

There is a lot of way to organize your Json schema: I recommand to go to this link http://json-schema.org/understanding-json-schema/ .
For example, one of my favourite is to use the "definitions" properties, that help you to define reusable data properties schema.

```
{
    "$schema": "http://json-schema.org/draft-07/schema",
    "definitions": {
        "text_template": {
            "type": "string"
        }
    },
    "type": "object",
    "properties": {
        "first_name": {
            "$ref": "#/definitions/text_template"
        },
        "last_name": {
            "$ref": "#/definitions/text_template"
        },
    },
    "additionalProperties": true
}
```
