# Django: comprendre les vues en profondeur



---
## De WSGI à vos premières vues basées sur les classes

---
### Etape 1: répondre à une requête avec une réponse

---

```python
def application(environ, start_response):
    """Application WSGI interagissant avec le serveur 
    d'application."""
    response_body = 'Bonjour à tous !'
    status = '200 OK'
    response_headers = [
        ('Content-Type', 'text/plain; charset=utf8'),
    ]
    start_response(status, response_headers)
    yield response_body.encode('utf8')
```

---

```python
from wsgiref.util import setup_testing_defaults
from wsgiref.simple_server import make_server

def main():
    """Point d'entrée principal de notre application."""
    with make_server('localhost', 8765, application) as httpd:
        print(f"Application servie sur localhost:8765...")
        httpd.serve_forever()
```

---
### Etape 2: servir un template html

---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" 
        content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <h1>Bonjour à tous !</h1>
</body>
</html>
```

---

```python
from pathlib import Path

# ...

with Path('templates/home.html').open() as template:
    content = template.read()
    response_body = content.format(
        title="Bienvenue à ce webinaire !"
    )
```

---
### Etape 3: ajout d'un moteur de template

---
```
$ pipenv install jinja2
```

---
```
├── Pipfile
├── Pipfile.lock
├── README.md
└── website
    ├── __init__.py
    ├── __main__.py
    └── templates
        └── home.html
```
---
```python
from jinja2 import (
    Environment, PackageLoader, select_autoescape
)

env = Environment(
    loader=PackageLoader('website', 'templates'),
    autoescape=select_autoescape(['html']),
)
```
---
```html
<body>
    <h1>{{ title }}</h1>
</body>
```

---

```python
template = env.get_template('home.html')
yield template.render(title="Salut").encode('utf8')
```

---

---
## Thierry Chappuis
Développeur et formateur

---
Email: thierry.chappuis@placepython.com
Twitter: @tchappui
