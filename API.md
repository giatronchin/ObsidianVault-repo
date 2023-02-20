# Django DRF (Django REST framework)

Django is great for handling data model and with the power of DRF devs can achive data-**serialization** - meaning converting database retrived data to JSON/XML format for API response.

![[Pasted image 20230220063550.png]]

Startup of the whole project and folder-tree:
```bash
mkdir little-lemon
cd little-lemon
pipenv install django
pipenv shell

# Create Project-tree and App-tree
django-admin startproject BookList .
cd BookList
python3 manage.py startapp BookListAPI

# Install DRF
pip3 install djangorestframework 

or 

pipenv install djangorestframework
```

Add '`rest_framework`' into `settings.py` at the **app level** within the **INSTALLED_APPS** list.

```python
# settings.py @app-level

INSTALLED_APPS = [
	'django.contrib.admin',
	...
	'BookListAPI',
	'rest_framework'
]
```

On the other hand, DRF implements views in a slightly different way:

```python
from django.shortcuts import render
from rest_framework.response import Response
from rest_framework import status
from rest_framework.decorators import api_view

@api_view()
def books(requests):
	return Response('list of the books', status=status.HTTP_200_OK)
```

Noticed different pieces:
- `api_view` decorator needed in order to use Response()
	- Accept also an array containing all the *methods accepted* by the API
	- Allow implementation of **Throttling** and **Rate-Limiting**
	- Allow implementation for **Authentication** checking
- `Response` instead on usual django.HttpResponse()
- **status** in a *human readable format* made available by DRF

<mark>Note</mark>:Both `api_view` decorator and `Response` method render the api output of as a need debug view in the browser. In production this probably would be replace with `HttpResponse` (removing the api_view) decorator.

Of course we need to create a urls.py at app level and include this at the urls.py at the project level as seen for Django web app project.

```python
# urls.py @app
from . import views
from django.urls import path

urlspatterns = [
	path('books/', views.books),
]
```

```python
# urls.py @project
from django.contrib import admin
from django.urls import path, include

urlspatterns = [
	path('admin/', admin.site.urls),
	path('api/,'include('BookListAPI.urls')
]
```
