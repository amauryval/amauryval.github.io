---
title: "Les tests avec Python"
last_modified_at: 2020-10-25T22:30:49
categories:
  - Tutos
tags:
  - Python
  - Test
toc: true
toc_sticky: true
toc_label: "Dans cette page..."
---


<figure style="width: 0px; visibility: hidden;" class="">
  <a href="/assets/images/memes/code.jpg"><img src="/assets/images/memes/code.jpg"></a>
</figure>


La réalisation de tests est une nécessité absolue et fait partie totalement du processus de développement dans la mesure où il permet de valider la pertinence des résultats d'un processus.
Les avantages sont très nombreux :
- détection des erreurs ;
- éviter les regessions et ainsi faciliter la maintenance du code ;
- un complément à la documentation en faisant office d'exemples.

Généralement,  sont réalisés une fois la fonction faites et si seulement le temps le permet...  Malheureusement, les tests restent trop souvent une tâche secondaire... "Quand on aura le temps"... Pourtant le gain en terme de sérénité, de qualité et de maintenance est très important. 
Il existe différentes philosophie pour l'implémentation des tests, notamment des méthodes qui mettent les tests au centre du développement. Par exemple, pour le cas des tests unitaires, on peut citer le [Test Driven Development](https://fr.m.wikipedia.org/wiki/Test_driven_development) (TDD) qui consiste, grossièrement, à écrire les tests avant de développer un processus. Ici les tests deviennent le coeur de l'activité de développement permettant ainsi de contraindre dès le départ l'implémentation d'une fonction. Cela a pour conséquence une amélioration importante de la qualité du code :
- systématiquement testé ;
- produit des résultats prévisibles ;
- impose de réfléchir à l'implémentation, l'architecture du logiciel.
Il est évident que les tests au départ doivent couvrir la totalité des cas possibles..

Enfin, les tests doivent être lancés systématiquement à chaque modifications du code, notamment avec la mise en place d'un système d'intégration continue (CI), mais là c'est un autre vaste sujet !

On parle très souvent des tests unitaires, mais il existe aussi les tests fonctionnels. Les premiers sont au cœur du logiciel et souhaitent tester les plus petites briques de ce dernier. Les seconds s'attardent à tester des fonctionnalités composées d'un ensemble de fonctions, ou encore un comportement plus global. Il existe donc différents scopes dans les tests, à définir selon ses besoins.
Ici nous resterons centrés sur le développement des tests en général, la technique restant la même.

Dans cet article, nous allons jouer avec les tests unitaires en Python avec la librairie Pytest

# Installation

``` Bash
pip install pytest
```


TODO:
 * pytest-cov
 * pytest-html

# Au départ

## Organisation des répertoires et fichiers

Tout d'abord, il est nécessaire de structurer correctement l'arborescence de sa libraire à tester.


Un répertoire parent "my_project" contenant  deux sous répertoires :
* _my_lib_ contenant nos fonctions
*  _tests_ contenant les tests

```
my_project
│   README.md
│
└───my_lib
│   │   main.py
│   │   ...
│   
└───tests
    │   fixture.py
    │   conftest.py
    │   test_my_first_test.py
    │   test_my_second_test.py
    │   ...
```

## Le répertoire "my_lib"

_my_project/my_lib_ est le répertoire contenant la librairie ou le processus dévelopé. Ici nous avons créé un fichier main.py contenant une fonction :

_main.py_ :

```python
def multiply_number(number):
    return number * 2

if __name__ == '__main__':
    multiply_number()
```


## Le répertoire "tests"

_my_project/test_ est le répertoire qui contient l'ensemble de nos tests.
Si on reste sur une configuration simple, on y trouve 2 types de fichiers : 
* conftest.py
* différents fichiers test_*.py contenant les tests

### Le fichier conftest.py

Le fichier "conftest.py" est le point de départ de nos tests. En effet c'est le premier fichier utilisé par Pytest.

Il peut contenir un certains nombre d'éléments :
* des fonctions globales à l'ensemble des tests qui seront lancés avant ou après les tests (ou certains tests, cf le "scope") ;
* des fixtures, qui correspondent aux données d'entrée utilisés par les tests

Éventuellement, il peut ne rien contenir, du moins uniquement servir d'import à Pytest, aux fixtures et aux éventuelles autres fonctions... Cela dépendra de vos besoins et contraintes.

Exemple conftest.py :
```python
import Pytest

@pytest.fixture
def a_number():
    return 42
```

### Les fixtures


Dans notre configuration, nous avons un fichier "fixture.py". Comme son nom l'indique, il se destiné au stockage de nos fixtures (ou données de tests).
Une fixture est une méthode spéciale, et décorée par Pytest, contenant les données/fonctions d'entrée de vos futurs tests.

La fixture est un élément clé dans Pytest. Il ne doit pas être simplement réduit à un simple outil pour gérer les entrées de ses tests. Ce composant est beaucoup plus subtil et offre de nombreuses possibilités (TODO).

fixture.py :
```python
import Pytest

@pytest.fixture
def a_number():
    return 42

@pytest.fixture
def an_other_number():
    return 33
```

Comme évoqué précédemment, il est possible de les intégrer directement dans le fichier "conftest.py". Mais il semble préférable de cloisonner, pour des questions de maintenabilité, les différents éléments de nos tests : les fonctions générales, les données d'entrée...

À présent, il faut importer "fixture.py" dans "conftest.py" afin que les fixtures soient prises en compte

conftest.py :
```python
import Pytest

from fixture import a_number
from fixture import an_other_number

```

### Les fichiers de tests

Enfin, nous avons les fichiers contenant nos tests. La convention consiste à nommer ces fichiers, ou plus exactement des _modules_ selon ce schéma test_*.py ; ceci afin d'être reconnus par Pytest.

Ici nous avons donc choisi de créer 2 modules : 
* test_my_first_test.py
* test_my_second_test

Pour créer un test, il nous faut définir des méthodes (ou class) dans lesquelles nous allons écrire du code visant à tester quelque chose...

Là aussi, le nom des méthodes doit respecter le motif suivant "test_*()" afin d'être détecté comme un test.
Nous allons voir également comment utiliser les fixture précédemment créées

test_my_first_test.py :

```python
import Pytest

# import de la fonction à tester
from my_lib.main import print_numbers

# premier test avec l'appel de la fixture définie : a_number
def test_my_first_number(a_number):
    result = multiply_number(a_number)
    # vérification de la valeur attendue avec un assert
    assert result == 84

# premier test avec l'appel de la fixture définie : a_other_number
def test_my_first_number(a_other_number):
    result = multiply_number(a_other_number)
    # vérification de la valeur attendue avec un assert
    assert result == 66
```

On remarque que les fixtures ne demandent pas l'usage des "()" permettant d'appeler la méthode.
On utilise le mot-clé "assert" afin de valider ou non le test, plus exactement la condition. Évidemment, il est possible d'avoir plusieurs "assert". Si un des assert échoue (False),  Pytest indiquera que le test a échoué.
 
Il est possible d'ajouter des décorateurs à nos méthodes de tests afin de changer leur comportements :
 * lancer un test selon une condition
 * s'attendre qu'un test échoue

Plus d'infos [ici](https://docs.pytest.org/en/latest/skipping.html) 

Le module _test_my_second_test_ est vide. On n'ajoutera pas de tests : il est uniquement présent pour indiquer que l'on peut en ajouter d'autres.


## Lancement des tests

TODO: 
    * exemples avec arg
    * coverage arg

```
python -m pytest tests/
```
 
## Résultats

TODO: 
    * pytest-html
    * coverage

 

# Allons plus loin avec les fixtures

Il est un peu grossier de dire que les fixtures ne sont que des données utilisables dans les tests. Elles permettent également d'améliorer la mécanique de tests, d'optimiser l'exécution et réaliser du refactoring.

## les scopes

Il est possible d'étendre les fonctionnalités des tests :
 * ajouter des méthodes classiques utilisables par l'ensemble des tests (factorisation)
 * ajouter des fixtures
 
 Mais aussi, et c'est le sujet que nous allons développer :
 * exécuter du code avant et/ou après chaque test (équivalent des méthodes setUp() et tearDown() de unitest). Ce sera l'occasion de jouer avec les scopes : _module, session, class, fonction_ (scope par défaut).

Illustrons cela vec Flask et Selenium...

## Exemple avec Flask

Pour tester des routes flask, il faut être capable d'initialiser l'API avant le lancement du test.

On utilisera cette fixture dans le fichier "conftest.py"

conftest.py avec une API blueprint :
```python
import pytest

from flask import Flask
# import de l'api
from my_app.main import my_api

# fixture permettant d'initialiser l'API Flask pour l'ensemble des tests (scope="session")
@pytest.fixture(scope="session")
def flask_client():

    app = Flask(__name__)
    
    app.register_blueprint(my_api, url_prefix="")  # important dans le cas d'une API blueprint
    resume_api_client = app.test_client()
    print("API starter !")
    # avant les tests
    yield resume_api_client
    # après le test

    # le print sera exécuté une fois les tests terminés
    print("tests done ! API closed !")
    
```

On note que cette fixture _flask_client()_ a été décorée avec le scope "session" pour qu'elle soit appliquée avant le lancement et à la fin de tous les tests (ceux présents dans les fichiers "test_*.py).

Ici, _Yield_ retourne le résultat de la méthode _flask_client()_, qui sera utilisable dans la méthode de test:

test_my_route_flask.py :
```python
import pytest

# ici en argument de la fonction on appelle la fixture "flask_client", qui est aussitôt exécutée pour renvoyer l'instance flask de notre API
def test_a_flask_route(flask_client):
    response = flask_client.get("/", content_type="html/text")
    assert response.status_code == 200
```

Et si on avait utiliser d'autres scopes?
 * _fonction_: l'API aurait été démarrée et arrêtée après chaque test ;
 * _module_ : l'API aurait été démarrée et arrêtée pour chaque fichier de tests.


## Exemple avec Selenium

Selenium est une librairie python qui permet d'effectuer des actions automatiques sur un navigateur web. Elle peut ainsi a automatiser des actions mais aussi tester des interfaces web.

Pour réaliser des tests avec Selenium, il faut demander à pytest de lancer le navigateur (ici Chrome) avant le lancement du test.

Au préalable, il faut:
 * installer le navigateur sur lequel on souhaite effectuer les tests
 * installer le web driver associé (même version que le navigateur installé)

Pour chrome :
    * [https://chromedriver.storage.googleapis.com/[version]/chromedriver_linux64.zip]


On utilisera cette fixture, dans le fichier "conftest.py", avec un scope _class_, car on souhaite que la fixture soit initialisée pour chaque classe de test.

conftest.py :
```python
import pytest

from selenium import webdriver

# scope défini a la classe pour que cette fixture soit instancié pour chaque classe de test
@pytest.fixtures(scope="class")
def chrome_browser(request):

    driver = webdriver.Chrome("chromedriver")
    # driver est l'instance nous permettant de jouer avec le contenu du navigateur
    driver.maximize_window()
    
    # on ajoute l'attribut "driver" à la méthode qui appelle cette fixture
    request.cls.driver = driver

    # avant le test
    yield
    # après le test

    driver.close()  # on ferme le navigateur une fois le ou les tests, contenus dans la class, sont terminés
```

On note que la méthode (ou plutôt fixture) _chrome_browser()_ a été décorée pour qu'elle soit appliquée (si appelée) au lancement de chaque class de test (celles présentes dans les fichiers "test_*.py). Les classes de test acquiert ainsi des attributs correspondant aux variables définies dans la fixture.

test_my_website.py :
```python
import pytest

# ici en argument de la class on appelle la fixture "chrome_browser", qui est aussitôt exécutée pour créer l'attribut "driver" (entre autres) au sein de la class de test "TestWebSite"
@pytest.mark.usefixtures("chrome_browser")
class TestWebSite:

    def test_google_url(self):
        url = "www.google.com"   # on aurait dû créer une fixture! (uniquement pour l'exemple)
        self.driver.get(url)  # on retrouve driver qui a été défini dans la fixture chrom_browser
        assert self.driver.title == "Google"

    def test_bing_url(self):
        url = "www.bing.com"  # on aurait dû créer une fixture! (uniquement pour l'exemple)
        self.driver.get(url)
        assert self.driver.title == "Bing"
```

On remarque que les variables de notre fixture _chrome_browser_ ont été injectées dans la classe de test _TestWebSite_ et sont utilisables. Nous avons ainsi initialiser une fixture utilisable n'importe quand et n'importe où dans notre classe de test. Une fois les tests terminé, le "driver" sera fermé.
