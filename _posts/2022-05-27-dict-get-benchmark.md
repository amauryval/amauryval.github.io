---
title: "Benchmark - Récupérer la valeur d'un dictionnaire"
last_modified_at: 2022-05-27T21:08:24
categories:
  - Benchmark
tags:
  - Python
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---

Benchmark sur différentes méthodes pour récupérer la valeur d'un dictionnaire:

<figure style="width: 200px" class="">
  <a href="/assets/images/memes/code.jpg"><img src="/assets/images/memes/code.jpg"></a>
</figure>

```python
import timeit
import collections

# get() call
def with_get():
    d = {'foo': "bar"}
    x = d.get('foo', None)

# Brackets call
def with_brackets():
    d = {'foo': 'bar'}
    x = d['foo']

def with_custom_missing_class():
    class myDict(dict):
        def __missing__(self, key):
            return None
    d = myDict({'foo': 'bar'})
    x = d['foo']

def with_caching_of_function_lookup():
    d = {'foo': 'bar'}
    get = d.get
    x = get('foo', None)

def with_brackets_and_in_operator():
    d = {'foo': 'bar'}
    x = d['foo'] if 'abc' in d else None

def with_brackets_and_try_returned():
    d = {'foo': 'bar'}
    try:
        x = d['foo']
    except KeyError:
        pass

def with_brackets_and_try_except_returned():
    d = {'foo': 'bar'}
    try:
        x = d['bar']
    except KeyError:
        pass

def with_collections_default_dict():
    d = collections.defaultdict(lambda: None)
    d['foo'] = "bar";
    x = d['XXX']

timeit.Timer(with_get).timeit(number=50000)
timeit.Timer(with_brackets).timeit(number=50000)
timeit.Timer(with_custom_missing_class).timeit(number=50000)
timeit.Timer(with_caching_of_function_lookup).timeit(number=50000)
timeit.Timer(with_brackets_and_in_operator).timeit(number=50000)
timeit.Timer(with_brackets_and_try_returned).timeit(number=50000)
timeit.Timer(with_brackets_and_try_except_returned).timeit(number=50000)
timeit.Timer(with_collections_default_dict).timeit(number=50000)

```

Résultats - Python 3.9:

| func                                       | duration                     |
| -------------------------------------------|:----------------------------:|
| with_get                                   | 0.010526700000013989         |
| with_brackets                              | 0.00805250000007618          |
| with_custom_missing_class                  | 0.4602535999999873           |
| with_caching_of_function_lookup            | 0.011689899999964837         |
| with_brackets_and_in_operator              | 0.008910399999990659         |
| with_brackets_and_try_returned             | 0.008611599999994723         |
| with_brackets_and_try_except_returned      | 0.017046899999968446         |
| with_collections_default_dict              | 0.030047200000012708         |


Résultats - Python 3.10:

| func                                       | duration                     |
| -------------------------------------------|:----------------------------:|
| with_get                                   | 0.0169717710000441           |
| with_brackets                              | 0.01423030299997663          |
| with_custom_missing_class                  | 0.3670625449999534           |
| with_caching_of_function_lookup            | 0.021030869000014718         |
| with_brackets_and_in_operator              | 0.018795825000097466         |
| with_brackets_and_try_returned             | 0.014779266999994434         |
| with_brackets_and_try_except_returned      | 0.026639114000090558         |
| with_collections_default_dict              | 0.042068502000120134         |


