# REST API's with Django and Django Rest Framework

#### Third Part - Bulding API in Django with Djanto Rest Framework (Finally!!)

---
### Introdução
- **Objetivo**: apresentar como construir em Django uma RESTfull API com o auxílio do Django Rest Framework (DRF)
- Instalação do DRF|
- Criação da app com o Django e DRF|
- Construção da API: models, views, serializers e urls |

---
### Intalação do Django Rest Framework

@title[Um simples `pip install`]

Um simples `pip install`

```bash
(.venv) drf_api> pip install djangorestframework
...
Installing collected packages: djangorestframework
Successfully installed djangorestframework-3.8.2
(.venv) drf_api>
```

@[1]
@[2-4]
@[5]

---
### Mantendo os requisitos da nossa aplicação

@title[Atualizando nosso requirements.txt]

Atualizando nosso requirements.txt

```bash
(.venv) drf_api> pip freeze
dj-database-url==0.5.0
Django==2.0.5
djangorestframework==3.8.2
gunicorn==19.8.1
psycopg2==2.7.4
python-decouple==3.1
pytz==2018.4
(.venv) drf_api> pip freeze > requirements.txt
(.venv) drf_api>
```

@[1]
@[2-8]
@[4]
@[9-10]


---
### O projeto no Django

@title[Projeto vmtodo e app core criados na apresntação anterior]

Projeto vmtodo e app core criados no Django
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
@[2-7]
@[8-21]


---
#### Criando nossa aplicação no Django

@title[A app drf]

A app *drf*

```bash
(.venv) drf_api> python manage.py startapp drf
(.venv) drf_api>
```

@[1]
@[2]

---
#### Nossa estrutura de arquivos após de nossa nova app

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
    │   (...)
    ├── drf
    │   ├── admin.py
    │   ├── apps.py
    │   ├── __init__.py
    │   ├── migrations
    │   ├── models.py
    │   ├── serializers.py
    │   ├── tests.py
    │   └── views.py
    │   (...)
```
@[10-18]

---
#### Instalando nossa app e o DRF no Django

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
    'rest_framework',
    'vmtodo.core',
    'vmtodo.drf',
]

```

@[1-9]
@[10]
@[11]
@[12]

---
#### Sobre a Desabilitação do CSRF

@title[settings.py - Middleware]

settings.py - Middleware

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
]
```

@[5]

---
#### Criando nosso Model

@title[models.py]

models.py

```python
from django.db import models


class Tasks(models.Model):
    task = models.CharField(max_length=100)
    disable = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True, )

    class Meta:
        ordering = ('-created_at',)
```

@[1]
@[4-7]
@[9-10]

---
#### Configurando nossas rotas (endpoints)

@title[urls.py]

urls.py

```python
from django.contrib import admin
from django.urls import path

from vmtodo.core.views import get_post_tasks, get_delete_put_task_datail
from vmtodo.drf.views import TaskList, TaskDetails

urlpatterns = [
    path('drf/api/v1/tasks/', TaskList.as_view()),
    path('drf/api/v1/tasks/<int:pk>', TaskDetails.as_view()),
    path('api/v1/tasks/', get_post_tasks),
    path('api/v1/tasks/<int:pk>', get_delete_put_task_datail),
    path('admin/', admin.site.urls),
]
```

@[1-2]
@[4]
@[5]
@[7-13]
@[8]
@[9]
@[10-13]

---
#### O coração de nossa API

@title[core/views.py]

core/views.py

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


def get_delete_put_task_detail(request, pk):
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

@[1-4]
@[7-20]
@[23-56]

---
#### O coração de nossa API - Views e Serializers

@title[serializers.py]

serializers.py

```python
from rest_framework import serializers
from vmtodo.drf.models import Tasks


class TaskSerializer(serializers.ModelSerializer):
    class Meta:
        model = Tasks
        fields = ('id', 'task', 'disable', 'created_at')

```
@[1] (Impotamos o serializers do drf)
@[2] (Importamos nosso models)
@[5-8] (Criamos a Classe TaskSerializer que ficará responsável pela serialização e deserialização)
@[6] (Criamos uma Classe Meta)
@[7] (Definimos qual o model a ser utilizado)
@[8] (Definimos quais os campos queremos que sejam utilizados)
---
#### O coração de nossa API - Views e Serializers

@title[drf/views.py]

drf/views.py

```python
from vmtodo.drf.models import Tasks
from vmtodo.drf.serializers import TaskSerializer
from rest_framework import generics


class TaskList(generics.ListCreateAPIView):
    queryset = Tasks.objects.all()
    serializer_class = TaskSerializer


class TaskDetails(generics.RetrieveUpdateDestroyAPIView):
    queryset = Tasks.objects.all()
    serializer_class = TaskSerializer

```
@[1](Importamos nosso models)
@[2](Importamos nosso serializer)
@[3](Importamos a classe Generics do DRF)
@[6-8](Definmos nossa classe para o GET e POST)
@[7](Construímos uma queryset)
@[8](Definimos qual serializer utilizar)
@[11-13](Definimos nossa classe para o Get, PUT e DELETE)
@[12](Construímos uma queryset)
@[13](Definimos qual serilizer utilizar)
---
# Vamos aos testes!!


---
# Obrigado
