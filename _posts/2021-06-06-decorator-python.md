---
title: "Python et les décorateurs"
last_modified_at: 2021-06-06T22:37:24
categories:
  - Topic
tags:
  - Python
  - Decorateur
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---


Les décorateurs, "c'est comme une boîte de chocolat"... 

![](https://media.giphy.com/media/z5EthizqZYAFi/giphy.gif)


## Mais avant, à quoi ça sert ?

Un décorateur permet d'étendre la logique d'une méthode, ou encore de lui superposer d'autres comportements. On peut prendre pour exemple un décorateur assez classique "time it" qui consiste à déterminer le temps d'exécution d'une méthode donnée. Le décorateur est ainsi applicable très facilement à toutes les méthodes et nous permet de respecter le DRY (_Don't Repeat Yourself_).

Quelques exemples sont disponibles [ici](##-D'autres-applications)

## Le principe

Effectivement, pour expliquer le fonctionnement d'un décorateur, nous pouvons jouer avec le principe d'une boîte de chocolat !
À priori :
* on ouvre la boîte ;
* on prend un chocolat pour le manger ;
* on referme la boîte

Dans cet exemple, on va partir du principe que la fonction consiste à manger le chocolat. Les interactions avec la boîte seront réalisées à l'aide d'un décorateur. Finalement un décorateur n'est qu'un enrobage d'une fonction, qui permettra l'injection de code avant et/ou après l'exécution de la fonction.

## A quoi ça ressemble ?

Un décorateur... décore une fonction de la manière suivante avec le caractère "@" :

```python
@chocolate_box
def eat_a_chocolate():
    return "I'm eating a chocolate!"
```

On note que la méthode n'a pas d'argument : nous reviendrons sur ce point dans [ce paragraphe](##Les-arguments)

Le décorateur_@chocolate_box_actions_ doit être perçu comme une simple fonction qui, en gros, va prendre en argument la fonction _eat_a_chocolate()_.

## Implémentation

### Au départ ?

Un décorateur s'implémente de la manière suivante :

```python
import functools

def chocolate_box(func): 

    @functools.wraps(func)
    def wrapper_box():

        print(">> Open the chocolate box")
        # avant l'exécution de la fonction

        output_string = func()  # sera lancée la fonction eat_a_chocolate()

        # après, une fois que la fonction s'est exécutée
        print(output_string)

        print(">> Close the chocolate box")

    return wrapper_box
```

Exécutons, maintenant, le code suivant :

```python
@chocolate_box
def eat_a_chocolate():
    return "I'm eating a chocolate"

eat_a_chocolate()
```

Le résultat :

```bash
>> Open the chocolate box
I'm eating a chocolate
>> Close the chocolate box
```

### Wrapper la fonction à décorer !

On note la présence du décorateur _@functools.wraps(func)_. Oui, on utilise un décorateur pour créer un décorateur... Ce décorateur est facultatif,  néanmoins est une bonne pratique, car l'usage d'un décorateur a l'inconvénient de faire perdre les caractéristiques de la méthode décorée (nom, la docstring, les arguments...) au profit de celles de la méthode implémentant ce décorateur.

Si on tente d'accéder aux caractéristiques de la méthode _eat_a_chocolate_ utilisant le décorateur _@functools.wraps(func)_, nous obtiendrons le résultat suivant :

```python
print(eat_a_chocolate.__name__)
```

```bash
'eat_a_chocolate'
```

Si nous n'utilisons pas le décorateur _@functools.wraps(func)_ : 

```python
import functools

def chocolate_box(func):

    # On commente le décorateur
    # @functools.wraps(func)
    def wrapper_box():

        print(">> Open the chocolate box")
        # avant l'exécution de la fonction

        output_string = func()  # sera lancée la fonction eat_a_chocolate()

        # après, une fois que la fonction s'est exécutée
        print(output_string)

        print(">> Close the chocolate box")

    return wrapper_box

@chocolate_box
def eat_a_chocolate():
    return "I'm eating a chocolate"

print(eat_a_chocolate.__name__)
```

Avec pour résultat :

```bash
'wrapper'
```

C'est le nom de la méthode _wrapper_ présente dans le décorateur qui est retourné...

### Jongler avec plusieurs décorateurs

Il est possible d'ajouter plusieurs décorateurs ! Implémentons une fonction qui va nous permettre d'ouvrir et fermer le placard contenant la boîte de chocolat...

![](https://media.giphy.com/media/KE9cblgPK6EPS/giphy.gif)

```python
import functools

def cupboard(func):

    @functools.wraps(func)
    def wrapper_cupboard():

        print("> Open the cupboard")
        # avant l'exécution de la fonction

        func()

        # après, une fois que la fonction s'est exécutée
        print("> Close the cupboard")

    return wrapper_cupboard
```

Ajoutons ce nouveau décorateur :

```python
@cupboard
@chocolate_box
def eat_a_chocolate():
    return "I'm eating one chocolate"
```

Nous obtenons :

```bash
> Open the cupboard
>> Open the chocolate box
I'm eating one chocolate
>> Close the chocolate box
> Close the cupboard
```

Le premier décorateur _@cupboard_ enrobe le décorateur suivant _@chocolate_box_ qui encadre ensuite la fonction qui nous permet de manger un chocolat !

### Un décorateur n'est rien d'autre qu'une simple méthode

Finalement, un décorateur n'est qu'un autre moyen d'appeler une méthode avec l'utilisation du tag "@". On obtient exactement le même résultat en utilisant nos 2 décorateurs de la manière suivante, disons "comme d'habitude" :

```python
import functools

eat_chocolate_action = cupboard(  # le 1er décorateur
    chocolate_box(  # le 2nd décorateur
        eat_a_chocolate # la fonction (les 2 décorateurs sont également des fonctions....)
    )
)
# on apelle la fonction
eat_chocolate_action()
```

Nous obtenons le même résultat :

```bash
> Open the cupboard
>> Open the chocolate box
I'm eating one chocolate
>> Close the chocolate box
> Close the cupboard
```

## Les arguments

### Prise en charge des arguments de la méthode décorée

Une méthode peut avoir des arguments ; le décorateur doit prendre en compte cela lors de l'appel à la fonction (qui sera décorée)...

Ajoutons un argument a la fonction _eat_a_chocolate()_ qui va nous permettre de choisir soit un chocolat noir (que je préfère) ou au lait.

![](https://media.giphy.com/media/l2JdU5gv9TRLKl9jG/giphy.gif)


```python
@cupboard
@chocolate_box
def eat_a_chocolate(kind):
    return f"I'm eating a {kind} chocolate"

eat_a_chocolate("black")
```

Si on tente d'exécuter le code, on va obtenir l'erreur suivante indiquant un problème d'argument sur la méthode _wrapper_..

```bash
TypeError: wrapper() takes 0 positional arguments but 1 was given
```

Il est nécessaire de préciser le ou les arguments, au sein de __chaque décorateur__, au niveau de la ligne exécutant la fonction décorée, c'est-à-dire  _func()_ et la méthode wrappant la fonction. Par exemple pour le décorateur _cupboard_ où nous avons un seul argument à prendre en charge :

* _def wrapper_cupboard()_ devient ainsi _def wrapper_cupboard(arg_1)_.
* _func()_ devient ainsi _func(arg_1)_.

Si on souhaite apporter beaucoup plus de robustesse à nos décorateurs, nous indiquerons les décorateurs de la manière suivante, afin de prendre en charge toutes méthodes (simples ou d'une classe) et quel que soit le nombre d'arguments. Ainsi pour le décorateur _cupboard_ :

_func(*args, **kwargs)_ et _def wrapper_cupboard(*args, **kwargs)_

Ainsi, nous avons :

```python
import functools

def cupboard(func):

    @functools.wraps(func)
    def wrapper_cupboard():

        print("> Open the cupboard")
        # avant l'exécution de la fonction

        func()

        # après, une fois que la fonction s'est exécutée
        print("> Close the cupboard")

    return wrapper_cupboard


def chocolate_box(func):

    @functools.wraps(func)
    def wrapper_box(*args, **kwargs):

        print(">> Open the chocolate box")
        # avant l'exécution de la fonction

        output_string = func(*args, **kwargs)  # sera lancée la fonction eat_a_chocolate()
        # le résultat de la méthode décorée est récupéré

        # après, une fois que la fonction s'est exécutée
        print(output_string)

        print(">> Close the chocolate box")

    return wrapper_box
```

Exécutons le code suivant :

```python
@cupboard
@chocolate_box
def eat_a_chocolate(kind):
    return f"I'm eating a {kind} chocolate"
eat_a_chocolate("black")
```

```bash
> Open the cupboard
>> Open the chocolate box
I'm eating a black chocolate
>> Close the chocolate box
> Close the cupboard
```

### Prise en charge des arguments d'un décorateur

On rappelle qu'un décorateur est une simple fonction... Et comme toute fonction il peut avoir des arguments.

On a implémenté une fonction qui permet d'ouvrir un placard, mais on voudrait choisir le lieu où se trouve ce placard contenant cette fameuse boîte de chocolats... Donc, ajoutons un argument pour préciser ce lieu.
Il est nécessaire d'envelopper, avec une nouvelle méthode, notre wrapper pour prendre en charge cet argument _location_ :

```python
import functools

def cupboard(location): 

    def decorator(func):

        @functools.wraps(func)
        def wrapper_cupboard(*args, **kwargs):

            print(f"> Open the {location} cupboard")
            # avant l'exécution de la fonction

            func(*args, **kwargs)

            # après, une fois que la fonction s'est exécutée
            print(f"> Close the {location} cupboard")

        return wrapper_cupboard

    return decorator
```

Maintenant, exécutons notre code :

```python
@cupboard(location="kitchen")
@chocolate_box
def eat_a_chocolate(kind):
    return f"I'm eating a {kind} chocolate"

eat_a_chocolate("black")
```

Et voilà :

```bash
> Open the kitchen cupboard
>> Open the chocolate box
I'm eating a black chocolate
>> Close the chocolate box
> Close the kitchen cupboard
```

## Implémentation sous la forme d'une classe

Nous avons vu qu'un décorateur peut prendre la forme d'une méthode. On peut également l'implémenter sous la forme d'une classe ; forme qui, je trouve, est beaucoup plus intuitive et lisible, que sous la forme de méthode, notamment lorsque l'on souhaite gérer des arguments dans le décorateur.
Voici un décorateur équivalent à celui de notre _cupboard_.

```python
import functools

class Cupboard:

    def __init__(self, location):
        self._location = location

    def __call__(self, func):

        functools.wraps(func)
        def wrapper_cupboard(*args, **kwargs):

            self._opening_action()
            # avant l'exécution de la fonction

            func(*args, **kwargs)

            # après, une fois que la fonction s'est exécutée
            self._closing_action()

        return wrapper_cupboard

    def _opening_action(self):
        print(f"> Open the {self._location} cupboard")


    def _closing_action(self):
        print(f"> Close the {self._location} cupboard")

```

Enfin, exécutons à nouveau notre code :

```python

@Cupboard(location="kitchen")
@chocolate_box
def eat_a_chocolate(kind):
    return f"I'm eating a {kind} chocolate"

eat_a_chocolate("black")
```

Résultat :

```bash
> Open the kitchen cupboard
>> Open the chocolate box
I'm eating a black chocolate
>> Close the chocolate box
> Close the kitchen cupboard
```

## Exemples : 


Réexécuter une méthode si jamais une exception apparait. Utile lorsque l'on doit communiquer avec des évènements externes à Python: url web, évènement d'une application tierces (état de la GUI)...

```python
import time
from functools import wraps

def retry_at_exception(expected_exception, tries_count: int = 4, delay_between_tries: int = 3, delay_coeff_extender: int = 2):
    """
    Decorator useful to retry an method if an expected exception is returned

    :param expected_exception: The expected exception
    :type expected_exception: Exception
    :param tries_count: number of tries
    :type tries_count: integer
    :param delay_between_tries: mininum delay to retry
    :type delay_between_tries: integer
    :param delay_coeff_extender: To extend the delay of the next retry
    :type delay_coeff_extender: integer

    :return: the decorated func
    :rtype: func
    """
    def retry(func):

        @wraps(func)
        def func_retry(*args , **kwargs):
            current_tries_count, current_delay_between_tries = tries_count, delay_between_tries
            while current_tries_count > 1:
                try:
                    return func(*args, **kwargs)

                except expected_exception as e:
                    # only if the exception appears, we'll retry
                    print(f"{str(e)}: retrying in {current_delay_between_tries} seconds...")
                    time.sleep(current_delay_between_tries)

                    current_tries_count -= 1
                    # we are extending the delay betweeen each retry
                    current_delay_between_tries *= delay_coeff_extender

            return func(*args, **kwargs)

        return func_retry

    return retry
```