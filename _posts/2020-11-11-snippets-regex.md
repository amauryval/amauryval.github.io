---
title: "Snippets - Regex"
last_modified_at: 2020-11-11T12:07:24
categories:
  - Snippets
tags:
  - Regex
  - Python
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---

Un aide mémoire pour les regex...

<figure style="width: 0px; visibility: hidden;" id="img-header">
  <a href="/assets/images/memes/code.jpg"><img src="/assets/images/memes/code.jpg"></a>
</figure>

# string value extracteur into a dictionnary
```python
import re

def str_to_dict_from_regex(string_value, regex):
    pattern = re.compile(regex) 
    extraction = pattern.match(string_value)
    return extraction.groupdict()
```

# Database url extraction pattern

Example:
```
postgres://johndoe:597f99fb704c5259c@127.0.0.1:5432/db_name
```

```
.+:\/\/(?P<username>.+):(?P<password>.+)@(?P<host>[\W\w-]+):(?P<port>\d+)\/(?P<database>.+)
```
