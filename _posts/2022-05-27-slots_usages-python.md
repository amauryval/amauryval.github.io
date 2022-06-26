---
title: "Benchmark - Slots ou pas slots ?"
last_modified_at: 2022-05-27T21:08:24
categories:
  - Benchmark
tags:
  - Python
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
img_header: "/assets/images/memes/code.jpg"
short_text: "Benchmark pour évaluer l'intérêt de définir un slot dans une classe Python..."
---

Benchmark pour évaluer l'intérêt de définir un slot dans une classe Python:

<figure style="width: 0px; visibility: hidden;" id="img-header">
  <a href="/assets/images/memes/code.jpg"><img src="/assets/images/memes/code.jpg"></a>
</figure>

```python
import timeit
from sys import getsizeof


class SlottedPoint:
    __slots__ = ("x", "y")

    def __init__(self, x, y):
        self.x = x
        self.y = y



class NonSlottedPoint:

    def __init__(self, x, y):
        self.x = x
        self.y = y


def dictionary():
    return {
        "x": 1,
        "y": 2
    }

def check_func_performance(func: str, call_count: int = 1000000, repeat_count: int = 10):
    func_time = timeit.repeat(func, number=call_count, globals=globals(), repeat=repeat_count)
    func_time_value = sum(func_time) / repeat_count
    size = getsizeof(eval(func))

    return {
        "title": func,
        "duration": func_time_value,
        "object_size": f"{size} Byte(s)"
    }



results = check_func_performance('SlottedPoint(1, 2)')
print(results)

results = check_func_performance('NonSlottedPoint(1, 2)')
print(results)

results = check_func_performance('dictionary()')
print(results)

```

**Résultats - Python 3.9:**

| func                    | duration                | size               |
| ------------------------|:------------------------|:-------------------|
| SlottedPoint(1, 2)      | 0.3355669799999987      | 48 Byte(s)         |
| NonSlottedPoint(1, 2)   | 0.42160326000000625     | 48 Byte(s)         |
| dictionary()            | 0.19480267000001278     | 232 Byte(s)        |


**Résultats - Python 3.10:**

| func                    | duration                | size               |
| ------------------------|:------------------------|:-------------------|
| SlottedPoint(1, 2)      | 0.5916475000325591      | 48 Byte(s)         |
| NonSlottedPoint(1, 2)   | 0.5538422700483352      | 48 Byte(s)         |
| dictionary()            | 0.26400925999041647     | 232 Byte(s)        |