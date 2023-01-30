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

When the user first make an HTTP request it is intercepted by the URL dispatcher , namly the `urls.py` at the **project-level**. This file contains an object `urlpattern` which is basically a list of tuples that maps URL requested by the user and the view function to call.
- `urls.py` at the project-level often contains reference to the `urls.py` at the **app-level**
specified via `include(appname.urls)` statement.

```python
# urls.py @ project-level
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
		path('admin/', admin.site.urls),
		path('', include(restaurant.urls))
]
```
The views file has the responsability to handle the http request, implement the application logic and return an Http Response. 
_Note_: Django obtains the HttpRequest object from the context provided by the server. As a client request is received, Django’s URL dispatcher mechanism invokes a view that matches the URL pattern and passes this HTTPRequest object as the first argument so that all the request metadata is available to the view for processing.
```python
# views.py @ app-level
from django.shortcuts import render
from django.http import HttpResponse

def home(request):
	return HttpResponse("Welcom to Little Lemon restaurant!")
```
It essential to link the `urls.py` file at the project-level with the one at the app-level:
```python
# urls.py @ app-level
from django.urls import path
from . import views # Import the views.py file shown above

urlspatterns = [
		# Reference the 'home' function within the views.py file
		path('', views.home),
]
```

### Http Protocol

A request is mainly composed of different components or _headers_:
- **Method** - `GET`, `POST`, `UPDATE`, `DELETE`, `PUT` are the most common
- **Path** - The URL that target the requested resource
- **Protocol Version** - HTTP/1.1 and later
- **Parameters** - Further information used by the server to hadnle the requests
	- Host - Client software used by the customer/user to make the request
	- Accept-Language
```
GET / HTTP/1.1
Host: developer.mozilla.org
Accept-Language: en
```

Method different from GET can contains a _body_ of data. Most of the time this body is used by the HTTP response along with the **Status Code**:
- **Informational** - 100-199
- **Successful** - 200-299
- **Redirection** - 300-399
- **Client Error** - 400-499 (403 _Forbidden or Unauthorized_, 404 _Not Found_)
- **Server Error** - 500-599
```
HTTP/1.1 200 OK
Date: ....
Server: ...
Last-Modified: ...
Content-Length: 50
Content-Type: text/html
```


#### HttpRequest Object

- `request.method` - The view logic uses this attribute to identify how the client has approached the server. A browser submits its request using any HTTP methods or verbs – POST**,** GET**,** DELETE**,** and PUT.
`request.GET` and `request.POST`  The attributes return a dictionary-like object containing GET and POST parameters, respectively.
- `request.COOKIES` - Returns a dictionary of string keys and values.
- `request.FILES` - When the user uploads one or more files with a multipart form, they are present in this attribute in the form of UploadedFile objects. By appropriate logic in the view, these uploaded files are saved in the designated folder on the server.
- `request.user` - The request object also contains information about the current user. This attribute is an **object of django.contrib.auth.models.User** class. However, if the user is unauthenticated, it returns **AnonymousUser**.
- `request.has_key()` - it helps check whether the GET or POST parameter dictionary has a value for the given key.
- `request.META['key']` - return the value of a metadata header in the http request
- `request.schema` - return the schema or protocol used to make the request

#### HttpResponse Object
Unlike the HttpRequest object, which is supplied by the server’s context, the response object of **HttpResponse** class is _instantiated_ inside the **view** function before it is returned to the client.
Some of the main attributes and methods of the HttpResponse object are:
-  `status_code`: returns the HTTP status code corresponding to the response
-   `content`: returns the byte string of the response.
-   `__getitem__()`: method that returns the value of a header
-   `__setitem__()`: method used to add a header
-   `write()`: This method creates a file-like object.

```python
...
path('getuser/<name>/<id>', views.pathview, name='pathview'),
...
```
The `urls.py` pass the pattern mapping and the related variable the 'pathview' view function. This enable the views.py function to levarage on this variables as follow:

```python
from django.http import HttpResponse 

def pathview(request, name, id): 

    return HttpResponse("Name:{} UserID:{}".format(name, id))
```

**Path & Path-converters**

The URL pattern treats the identifiers in angular brackets (<..>) as the path parameters. By default, it parses the received value to the string type. Other path converters available are:

-   **str** - Matches any non-empty string and excludes the path separator, '**/**'. This is the default if a converter isn’t included in the expression. 
-   **int** - Matches zero or any positive integer and returns an int. For example:/customer/<int:id>
-   **slug** - Matches any slug string consisting of ASCII letters or numbers, including the hyphen and underscore characters.
-   **uuid** - Matches a formatted UUID.  For example: **075194d3-6885-417e-a8a8-6c931e272f00** and returns a UUID instance.
-   **path** - Matches any non-empty string and includes the path separator, '/'.

#### Query Strings
Query strings are an alternative approach to URL parameters for adding URL configurations.
The client URL may contain a query string linked to the endpoint, for example, http://localhost:8000/getuser/?name=John&id=1

A query string is a sequence of one or more **key=value** pairs concatenated by the **&** symbol. Each _key_ is the **query parameter**. 
These parameters are fetched by the view from the request object it receives. The request object’s GET property is a dictionary object.

The **key-value pairs in the query string** are added to the `request.GET` property. Hence, the name can be obtained with `request.GET[‘name’]` expression.

The next step is to add the following path in the urls.py file:
`path('getuser/', views.qryview, name='qryview')`

Declare the **qryview function** in the views.py file.
```python
def qryview(request): 
    name = request.GET['name'] 
    id = request.GET['id'] 
    return HttpResponse("Name:{} UserID:{}".format(name, id))
```

**Body parameters** are not encoded in the URL but they are passed as argument of POST method (`request.POST['body-parameter']`).
