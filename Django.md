#webdev #backend #python

```bash
python3 -m pip install --user --upgrade pip
pip3 install django
python3 -m pip install --user virtualenv #optional
```
Optional: work with virtual environments
```python
python3 -m pip install --user virtualenv

python3 -m venv env_name 

source env_name/bin/activate

deactivate
```

## Structures of the Django Project

- **Project** - the entire web application. It contains many _apps_.
- **Apps** (_features_) - submodule of a project used to implement a single functionality in the project

Apps are added via `startapp` command on `manage.py`:

`python manage.py startapp profile_feature`

This automatically add a folder in project folder-structer:

	my_site/             # project name
			manage.py
			comments/       # app 1    
			posts/          # app 2
			feed/           # app 3
			my_site/

For Django to recognize an app in a project, it must be added to the installed apps setting. Additionally, a Django app is a set of code that interacts with various parts of the framework.

The startproject is Django’s default project template. It creates the following file structure in the Python environment:

```bash
$ python -m django startproject demoproject

#in virtual env: django-admin startproject demoproject

$ ls /demoproject 
/demoproject 
│   manage.py 
│ 
└───demoproject <---- Python package for the project (__init__.py)
        asgi.py 
        settings.py 
        urls.py 
        wsgi.py 
        __init__.py
```
- `manage.py` is the same of `djang-admin`.
- `settings.py` - settings used by `django-admin` and `manage.py` utility. Some critical settings are shown below:
	- **Databases** - specifies the configuration of one or more databases to be used by the current Django application. By default, Django uses the SQLite database. 
	- **Debug** - By default, the development server runs in debug mode.
	- **Allowed hosts** - list of strings, by default, it is empty. Each string represents the fully qualified host/domain where this Django site can be served.
	- **Root URL Conf** - string pointing toward the urls.py module in which the project’s URL patterns are found.
	- **Static URL** - points to the folder where the static files, such as JavaScript code, CSS files and images, are placed.
```python
INSTALLED_APPS = [
		'django.contrib.admin' ,
		'django.contrib.auth' ,
		'django.contrib.contenttypes' ,
		'django.contrib.sessions' ,
		'django.contrib.messages' ,
		'django.contrib.staticfiles',
]

DEBUG = True
ALLOWED HOSTS
ROOT_URLCONF = 'demoproject.urls'
STATIC_URL

DATABASES = { 
    'default': { 
        'ENGINE': 'django.db.backends.sqlite3', 
        'NAME': BASE_DIR / 'db.sqlite3', 
    } 
    'default-2': {   
        'ENGINE': 'django.db.backends.mysql',   
        'NAME': 'djangotest',   
        'USER': 'root',   
        'PASSWORD': 'password',   
        'HOST': '127.0.0.1',   
        'PORT': '3306',            
    }
}
```

- `urls.py` - contains a list of object _urlpatterns_ that works as a lookup table for every incoming client requests. Default structure of the file looks like this:
```python
from django.contrib import admin 
from django.urls import path 

urlpatterns = [ 
	path('admin/', admin.site.urls), 
]
```
- `asgi.py`
- `wsgi.py`
As mentioned, a Django project folder can contain one or more apps. An app is also represented by a folder of a specific file system. The command to create an app is: 
```python
python manage.py startapp <name of app>

#Django manages the database operations with the ORM technique. Migration refers to generating a database table whose structure matches the data model declared in the app.
python manage.py makemigrations

#synchronizes the database state with the currently declared models and migrations
python manage.py migrate

python manage.py runserver #builtin dev server on the localhost on port 8000

python manage.py shell #opens up an interactive Python shell inside the project

```

### Step to build a Django Project

1. Create project directory
2. Create virtual environment: `python3 -m venv tutorial-env` & `sorce tutorial-env/bin/activate`
3. Create Django project: `django-admin startproject project-name`
4. Run development server: `python3 manage.py runserver`

-------
The `startapp` command option of the `manage.py` script creates a default folder structure for the **app** of that name.

Here’s how to create a **demoapp** in the _demoproject_ folder.

```python
python manage.py startapp demoapp
```
Here's the folder structure after the app creation:
```bash
tree demoproject
	demoproject 
	│   db.sqlite3 
	│   manage.py 
	│ 
	├───demoapp 
	│   │   admin.py 
	│   │   apps.py 
	│   │   models.py 
	│   │   tests.py 
	│   │   views.py 
	│   │   __init__.py 
	│   │ 
	│   └───migrations 
	│           __init__.py 
	│ 
	└───demoproject 
	    │   asgi.py 
	    │   settings.py 
	    │   urls.py 
	    │   wsgi.py 
	    │   __init__.py
```

- **view.py** - a view is a user-defined function that’s called when Django’s URL dispatcher identifies the client’s request URL and matches it with a URL pattern defined in the urls.py file. The auto-created views file is _empty_ at the beginning.

**Note**: For now you can just think of views as Python functions that generate the content that makes up the webpage.

- **urls.py** - This is not created by default at startapp execution. This file has the same scope as the urls.py file in the project folder (`demoproject/demoproject/urls.py`). Anyway urls can be both configured at project as well as at app level (referencing this file in project-level urls.py) as in the  code snippet below:
```python
# demoapp/urls.py
from django.urls import path 
from . import views 

urlpatterns = [ 
    path('', views.index, name='index'), 
]
```

```python
# demoproject/urls.py
from django.contrib import admin 
from django.urls import include, path 

urlpatterns = [ 
    path('demo/', include('demoapp.urls')),  
    path('admin/', admin.site.urls), 
]
```
- **models.py** - The data models required for processing in this app are created in this file. It is empty by default.
- **tests.py** - the tests to be run on the app in this file

Also the `settings.py` as to be updated accordingly whenever we add an _app_ to the project.

```python
# demoproject/settings.py
...
INSTALLED_APPS = [ 
    'django.contrib.admin', 
    'django.contrib.auth',  
    'django.contrib.contenttypes', 
    'django.contrib.sessions', 
    'django.contrib.messages', 
    'django.contrib.staticfiles', 
    'demoapp', 
]
...
```
![[Pasted image 20230125170448.png]]
A Django application consists of the following components: 

-   **URL dispatcher** (urls.py) - It defines the URL patterns. Each URL pattern is mapped with a view function to be invoked when the client's request URL is found to be matching with it
-   **View** (views.py)  - The view function reads the _path_, _query_, and _body parameters_ included in the client's request (HTTP or HTTPS) If required, it uses this data to interact with the models to perform CRUD operations.
-   **Model** (models.py _Python Class_) - Django migrates the attributes of the model class to construct a database table of a matching structure. Django's Object Relational Mapper helps perform _CRUD operations in an object-oriented way_ instead of invoking SQL queries. 
	- The **view** uses the **client's and the model's data** and **renders** its _response_ using a **template**.
-   **Template** (html) - folder of html template file mixed with Django Template Language code blocks to render dynamic portion of the page.