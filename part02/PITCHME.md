# REST API's with Django and Django Rest Framework

### Second Part - Bulding API only in Django

---
### Introdução
- Objetivo: apresentar como podemos construir em Django uma RESTfull API sem o auxílio de um framework auxiliar
- Configuração do ambiente e instalação das ferramentas |
- Criação do projeto e da app com o Django |
- Construção da API: models, views e urls |

---
### Configuração do ambiente

@title[O básico do ambiente]

O básico do ambiente

```bash
> mkdir drf_api
> cd drf_api
drf_api> python3 -m venv .venv
drf_api> source .venv/bin/activate
(.venv) drf_api>
```

@[1]
@[2]
@[3]
@[4]
@[5]


---
#### Instalando as ferramentas 

@title[O uso do pip]
O uso do *pip*

```bash
(.venv) drf_api> pip install Django
...
Installing collected packages: pytz, django
Successfully installed django-2.0.5 pytz-2018.4
(.venv) drf_api> pip install python-decouple
Collecting python-decouple
Installing collected packages: python-decouple
Successfully installed python-decouple-3.1
(.venv) drf_api> pip install dj-database-url
...
Installing collected packages: dj-database-url
Successfully installed dj-database-url-0.5.0
(.venv) drf_api> pip install gunicorn
...
Installing collected packages: gunicorn
Successfully installed gunicorn-19.8.1
(.venv) drf_api> pip install psycopg2
...
Installing collected packages: psycopg2
Successfully installed psycopg2-2.7.4
(.venv) drf_api>
```

@[1-4]
@[5-8]
@[9-12]
@[13-16]
@[17-21]


---
### Mantendo os requisitos da nossa aplicação

@title[Criando nosso requirements.txt]

Criando nosso requirements.txt

```bash
(.venv) drf_api> pip freeze
dj-database-url==0.5.0
Django==2.0.5
gunicorn==19.8.1
psycopg2==2.7.4
python-decouple==3.1
pytz==2018.4
(.venv) drf_api> pip freeze > requirements.txt
(.venv) drf_api>
```

@[1]
@[2-7]
@[8-9]


---
### Criando o projeto no Django

@title[Projeto vmtodo no Django]

Projeto *vmtodo* no Django
```Bash
(.venv) drf_api> django-admin startproject vmtodo .
(.venv) drf_api>
```

@[1]
@[2]

---
#### Criando nossa aplicação no Django

@title[A app core]

A app *core*

```bash
(.venv) drf_api> python manage.py startapp core
(.venv) drf_api>
```

@[1]
@[2]

---
#### Nossa estrutura de arquivos após as criações do projeto e da app core

@title[Estrutura dos Arquivos]

Estrutura dos Arquivos

```bash
(.venv) drf_api> tree
.
├── LICENSE
├── manage.py
├── README.md
├── requirements.txt
└── vmtodo
    ├── core
    │   ├── admin.py
    │   ├── apps.py
    │   ├── __init__.py
    │   ├── migrations
    │   │   ├── __init__.py
    │   ├── models.py
    │   ├── templates
    │   ├── tests.py
    │   └── views.py
    ├── __init__.py
    ├── settings.py
    ├── urls.py
    └── wsgi.py
```

@[1]
@[3]
@[4]
@[5]
@[6]
@[7-8, 18-21]
@[8-17]

---
#### Instalando nossa app no Django

@title[settings.py - Installed Apps]

settigs.py - Installed Apps

```python
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'vmtodo.core', ## Aqui inserimos nossa app!!!
]

```

@[1-9]
@[10]

---
#### Desabilitando o CSRF

@title[settings.py - Middleware]

settings.py - Middleware

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    # 'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

@[5]

---
### Configurações gerais do Django

- Obviamente que precisamos usar as libs que importamos com o *python-decouple* e o *dj-database-url* para fazer as configurações necessárias no Django, principalmente para a separação entre instâncias de nossa aplicação. Como isso é algo básico e já visto em outras Talks vamos só deixar mencionado.

---
#### Criando nosso Model e ModelForm

@title[models.py]

models.py

```python
from django import forms
from django.db import models


class Tasks(models.Model):
    task = models.CharField(max_length=100)
    disable = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True, )

    class Meta:
        verbose_name = 'Tarefa'
        verbose_name_plural = 'Tarefas'
        ordering = ('-created_at',)

    def __str__(self):
        return self.task


class TaskForm(forms.ModelForm):
    class Meta:
        model = Tasks
        fields = 'task disable'.split()

```

@[1-2]
@[5-8]
@[10-13]
@[15-16]
@[19-22]

---
#### Configurando nossas rotas (endpoints)

@title[urls.py]

urls.py

```python
from django.contrib import admin
from django.urls import path

from vmtodo.core.views import get_post_tasks, 
                              get_delete_put_task_datail

urlpatterns = [
    path('api/v1/tasks/', get_post_tasks),
    path('api/v1/tasks/<int:pk>', 
        get_delete_put_task_datail),
    path('admin/', admin.site.urls),
]
```

@[1-2]
@[4-5]
@[7-12]
@[8]
@[9-10]
@[11]

---
#### O coração de nossa API

@title[views.py]

views.py

```python
import json
from django.forms import model_to_dict
from django.http import JsonResponse
from vmtodo.core.models import Tasks, TaskForm


def get_post_tasks(request):
    if request.method == 'GET':
        obj = list(Tasks.objects.all().values())
        return JsonResponse(obj, safe=False)
    elif request.method == 'POST':
        form = TaskForm(request.POST)
        if form.is_valid():
            form.save()
            return JsonResponse(data=form.data, status=201)
        else:
            return JsonResponse(data={
                                'message': 'Invalid format'
                                }, 
                                status=400)


def get_delete_put_task_datail(request, pk):
    if request.method == 'GET':
        try:
            obj = model_to_dict(Tasks.objects.get(pk=pk))
            return JsonResponse(data=obj, safe=False)
        except Tasks.DoesNotExist:
            return JsonResponse(data={
                                'message': 'Task not found'
                                },
                                status=404)
    elif request.method == 'DELETE':
        obj = Tasks.objects.filter(pk=pk).delete()
        if obj[0]:
            data = {
              'message': 'Task was deleted with success!'
            }
        else:
            data = {
              'message': 'Object does not found!'
            }
        return JsonResponse(data=data, status=204)
    elif request.method == 'PUT':
        task = Tasks.objects.get(pk=pk)
        put_data = json.loads(request.body)
        put_data['id'] = pk
        form = TaskForm(instance=task, data=put_data)
        if form.is_valid():
            form.save()
            return JsonResponse(data=form.data, status=204)
        else:
            return JsonResponse(data={
                                'message': 'Invalid format'
                                },
                                status=400)
```

@[1] 
@[2]
@[3]
@[4]
@[7-20]
@[8-10]
@[9]
@[10]
@[11-20]
@[12]
@[13]
@[14]
@[15]
@[16-20]
@[23-55]
@[24-32]
@[26-27]
@[29-32]
@[33-43]
@[34]
@[35-38]
@[39-42]
@[43]
@[44-56]
@[45]
@[46-47]
@[48]
@[49-51]
@[52-56]

---
# Vamos aos testes!!


---
# Obrigado


