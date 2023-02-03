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
_Note_: notice that 'name' and 'id' variable have to be added to the pathview along with the request argument.

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

### Creating URLs and Views

In the `urls.py` file devs can reference the same view file and view.function for multiple URLs. On the other hand to avoid repeating the same `path()` entry we can use **regex** to build flexible URLs thanks to the `django.ulrs.re_path` module.

```python
# urls.py @ app-level
from django.urls import path, re_path
from . import views # Import the views.py file shown above

urlspatterns = [
		# Reference the 'home' function within the views.py file
		path('', views.home),
		path('index/<int: n>', view.index),
		re_path(regex, view.index),
]
```

It's important to use **namespace** to reference specifically view functions within a specific app.
Namespace can be specificied at the `path()` statement with a third argument **name**. This is especially helpful when we want to target a specific path in an app (via form for example).

```python
<form action="{% url 'login' %}" method="post">
#form attributes
<input type='submit'>
</form>

<form action="{% url 'demoapp:login' %}" method="post">
#form attributes
<input type='submit'>
</form>
```

### Error Handling with Django

To address an internal error or a client-request error with customs view devs has to immplement a specific handler in the `urls.py` at the **project-level**. The handler provided by django for this purpose are:
- `handler400 = 'myproject.views.handler400'`
- `handler403 = 'myproject.views.handler403'`
- `handler404 = 'myproject.views.handler404'`
- `handler500 = 'myproject.views.handler500'`

The custom views mapped are going to used a specific HttpResponse object-type to reply the client request (for custom error-handling function we have to pass the **exception** argument along with the request).
```python
from django.http import HttpResponse, HttpResponseNotFound

def my_view(request):
# ...
if condition==True:
	return HttpResponseNotFound('<h1>Page not found</h1>')
else:
	return HttpResponse('<h1>Page was found</h1>')
```
_Note_: implicitly the HttpResponseNotFound send a status code of 404 within the response.

Other type of HttpResponseError are:
- **HttpResponseBadRequest**
- **HttpResponseForbidden**

_Note_: **DEBUG** (within the `settings.py`) settings is by default set to True which means that standard error page will display error information that usually we don't want to be displayed to the end-user.

## Model
The object equivalent of a **database table** used in Django and acts as a single definitive source 
of information about your data. Model are class-instances defined in the module `djanog.db.models.Model` and each attribute represent a database field.
Django also define custom API to interact to the db/Model via python code instead of SQL statements.

Devs needs to add the app in `settings.py` under `INSTALLED_APPS` in order being able to create table based on Model class.

```sql
CREATE TABLE user(
	"id" serial NOT NULL PRIMARY KEY,
	"first_name" varchar(30) NOT NULL,
	"last_name" varchar(30) NOT NULL
);
```

```python
from django.db import models

class User(models.Model):
	first_name = models.CharField(max_length=30)
	last_name = models.CharField(max_length=30)
	db_table = 'table_name_of_your_choice' <------------------Edit to choose a custom table name
```
_Notice that there's no need for a primary key as specified in SQL code as Django takes care of it and creates it in background (devs can ovewrittes that on need)_.

#### CRUD
Django provides CRUD operations for Model class and their instances.
##### Create
```sql

INSERT INTO user(id, first_name, last_name) VALUES (1, "John", "Jones")
```

```python
new_user = User(id=1, "John", "Jones")
new_user.save()
```

##### Read
```sql
SELECT * FROM user WHERE id = 1;
```

```python
user = User.objects.get(id=1)
```

##### Update
```sql
UPDATE user
SET last_name = "Smith"
WHERE id = 1
```

```python
user = User.objects.get(id=1)
user.last_name = "Smith"
user.save()
```

##### Delete
```sql
DELETE FROM user WHERE id = 1
```

```python
User.objects.filter(id=1).delete()
```

## Object Model relationship
Recalling all kind of relationship that can take place in a [[SQL]] db, let's see how Django handle this in the OOP paradigm implemented. 

### One to one 
Here below an example of 2 table/model-object with a <mark>one-to-one</mark> relationship.

```python
# models.py ---> table_name: appname_college
class college(Model): 
    CollegeID = models.IntegerField(primary_key = True) 
    name = models.CharField(max_length=50) 
    strength = models.IntegerField() 
    website=models.URLField()
```

```python
# models.py ---> table_name: appname_principal
class Principal(models.Model): 
    CollegeID = models.OneToOneField( 
                College, 
                on_delete=models.CASCADE 
                ) 
    Qualification = models.CharField(max_length=50) 
    email = models.EmailField(max_length=50)
```

**Note**: by default, after migration take place, Django will name the tables in the database something like `appname_modelname`. 

The associated SQL syntax should look like:
```sql
CREATE TABLE "myapp_college" ("CollegeID" integer NOT NULL PRIMARY KEY, "name" varchar(50) NOT NULL, "strength" integer NOT NULL, "website" varchar(200) NOT NULL);

CREATE TABLE "myapp_principal" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "Qualification" varchar(50) NOT NULL, "email" varchar(50) NOT NULL, "CollegeID_id" integer NOT NULL UNIQUE REFERENCES "myapp_college" ("CollegeID") DEFERRABLE INITIALLY DEFERRED);
```
Notice that since there's a tight relationship between those object, the **foreign-key** has to implement an `on_delete` logic to process the behaviour in case the associated object in the primary model is deleted.

`on_delete` option can assume three different values:
-   **CASCADE:** deletes the object containing the `ForeignKey`
-   **PROTECT:** Prevent deletion of the referenced object.
-   **RESTRICT:** Prevent deletion of the referenced object by raising `RestrictedError`

### One to many
Here below an example of 2 table/model-object with a <mark>one-to-many</mark> relationship:
class Subject(models.Model): 
```python
class Subject(models.Model): 
    Subjectcode = models.IntegerField(primary_key = True) 
    name = models.CharField(max_length=30) 
    credits = model.IntegerField()
```

```python
class Teacher(models.Model): 
    TeacherID = models.IntegerField(primary_key=True) 
    subjectcode=models.ForeignKey( 
                Subject,  
                on_delete=models.CASCADE 
                  ) 
    Qualification = models.CharField(max_length=50) 
    email = models.EmailField(max_length=50)
```
Associated SQL syntax should look like:
```sql
CREATE TABLE "myapp_subject" ("Subjectcode" integer NOT NULL PRIMARY KEY, "name" varchar(30) NOT NULL, "credits" integer NOT NULL, "Qualification" varchar(50) NOT NULL, "email" varchar(50) NOT NULL);

CREATE TABLE "myapp_teacher" ("TeacherID" integer NOT NULL PRIMARY KEY, "Qualification" varchar(50) NOT NULL, "email" varchar(50) NOT NULL, "subjectcode_id" integer NOT NULL REFERENCES "myapp_subject" ("Subjectcode") DEFERRABLE INITIALLY DEFERRED);

CREATE INDEX "myapp_teacher_subjectcode_id_bef86dea" ON "myapp_teacher" ("subjectcode_id");```

### Many to many
At the end a <mark>many-to-many</mark> relationship example:
```python
class Teacher(models.Model): 
    TeacherID = models.IntegerField(primary_key=True) 
    Qualification = models.CharField(max_length=50) 
    email = models.EmailField(max_length=50)
```

```python
class Subject(models.Model): 
    Subjectcode = models.IntegerField(primary_key = True) 
    name = models.CharField(max_length=30) 
    credits = model.IntegerField() 
    teacher = model.ManyToManyField(Teacher)
```

### Migration

As said before Model are OOP representation (or **Object Relational Mapper**) of Django for the adjacent Data Model. To create a model devs just need to declare the model object class in the `models.py`, create a `migration_script` then apply all changes by executing the **migration** script:

```
python3 manage.py makemigrations   #Create migration script
python3 manage.py migrate
```

Django’s migration system has the following commands:

-  `makemigrations` - in the migrations package, a migration script `0001_initial.py`, is created.
- `migrate` - run the migrate command to apply the tasks in the migrations file to be performed.
- `sqlmigrate` - shows the SQL query or queries executed when a certain migration script is run.
- `showmigrations`
```bash
(django) C:\django\myproject>python manage.py showmigrations 
. . . 
. . . 
myapp 
[X] 0001_initial 
[ ] 0002_rename_name_person_person_name 
[ ] 0003_person_age 
. . .
```

Note: The initial migration (file numbered **0001**) has already migrated. The X mark is indicative of this. However, the next two migrations don’t show the X mark, which means they are pending. If we run the migrate command, both modifications will be reflected in the table structure.

!<mark>Important</mark>!
Models also needs to be **registred** in the `admin.py` file with the statement `admin.site.register(Model-Name)`.

```python
# models.py
from django.db import models

class Drinks(models.Model):
    drink = models.CharField(max_length=200)
    price = models.IntegerField()
```

```python
# admin.py
from django.contrib import admin
from .models import Drinks

admin.site.register(Drinks)
```

A **QuerySet** is a collection of objects for a given model used in Django. It represents a `SELECT` query. 
```python
from django.db import models  

class Customer(models.Model): 
    name = models.CharField(max_length=255) 

class Vehicle(models.Model): 
    name = models.CharField(max_length=255) 
    customer = models.ForeignKey( 
        Customer, 
        on_delete=models.CASCADE, 
        related_name='Vehicle' 
    )
```
To fetch all the objects, use the `all()` method of the Manager (**returns a list of objects**). Let's explore it with an example:
```python
#Syntax: model.objects.all() 

lst=Customer.objects.all() 
```
### Adding a model object
  `Create` an object of the Customer class. Give a string parameter to the constructor. The `save()` method of the `models.Model` class creates a row in the Customer table.
```python
from demoapp.models import Customer 

#Create a Customer object of the Customer Class
c=Customer(name="Henry") 
c.save() #create a row in the Customer Table

#------------------------OR-----------------------------#

# create == INSERT
Customer.objects.create(name="Hameed") # --->  Returns <Customer: Customer object (2)>
```
The `create()` method actually performs the `INSERT` operation of SQL.

### Fetch model objects
The `Customer.objects` gives the **Manager of the model**. It handles all the CRUD operations on the database table.
```python
c=Customer.objects.get(pk=2) 
c.name # ---> 'Hameed'
```

Devs can apply filters to the data fetched from the model. This is used to fetch objects satisfying the given criteria. In SQL terms, a `QuerySet` equates to a `SELECT` statement, it is like applying a `WHERE` clause.
```python
#Syntax: model.objects.filter(criteria)

mydata = Customer.objects.filter(name__startswith='H')
```

### Updating and removing a model object
To **update an object**, such as changing the Customer's name from Henry to Helen, _assign a new value to the name attribute_ and save.
```python
# Update
c=Customer.objects.get(name="Henry") 
c.name="Helen" 
c.save() 
```
Similarly, the `delete()` method of the model manager physically removes the corresponding row in the model's mapped table.
```python
# Delete
c=Customer.objects.get(pk=4) 
c.delete() # --->  (1, {'demoapp.Customer': 1}) 
```

## Model Field properties

A Field object has common properties in addition to the field-specific properties. 

- `primary_key` - This parameter is False by default. You can set it to True if you want the mapped field in the table to be used as its primary key. It's not mandatory for the model to have a field with a primary key. In its absence, Django, on its own, creates an IntegerField to hold a unique auto-incrementing number.
- `default` - You can also specify any default in the form of a value or a function that will be called when a new object is created.
- `unique` - If this parameter in the field definition is set to True, it will ensure that each object will have a unique value for this field.
- `choices` - If you want to create a drop-down from which the user should select a value for this field, set this parameter to a list of two-item tuples.

## Model Field types

The `django.models` module has many field types to choose from.

- **CharField:** This is the most used field type. It can hold string data of length specified by max_lenth parameter. For a longer string, use TextField.
- **IntegerField:** The field can store an integer between -2147483648 to 2147483647. There are BigIntegerField, SmallIntegerField and AutoFieldtypes as well to store integers of varying lengths.
- **FloatField:** It can store a floating-point number. Its variant DecimalField stores a number with fixed digits in the fractional part.
- **DateTimeField**: Stores the date and time as an object of Python's datetime.datetime class. The DateField stores datetime.date value.
- **EmailField**: It’s actually a CharField with an in-built EmailValidator
- **FileField**: This field is used to save the file uploaded by the user to a designated path specified by the upload_to parameter.
- **ImageField**: This is a variant of FileField, having the ability to validate if the uploaded file is an image.
- **URLField**: A CharField having in-built validation for URL.

# Forms
Almost all web app require input data from user. Django handle this concept with a Form class (`django.forms`). Here below an example of the class:

```python
# myapp/forms.py
from django import forms

class NameForm(forms.Form):
	your_name = forms.CharField(label='your name', max_length=100)
```
The `forms.Form` argument is essentialy what has been passed through th HTML form itself via http post request. 
`label` address the final aspects of the HTML form and `max_length` apply some basic validation onto it.
The HTML **template** can therefore benefit from this class declaration:
```html
<form action="/your-name/" method="post">
	{{ form }}
	<input type="submit" value="Submit">
</form>
```

The rendered HTML form code looks like:
```html
<form action="/your-name/" method="post">
	<label for="your name">Your name: </name>
	<input id="your name" type="text" name="your_name" maxlength="100" required>
		<input type="submit" value="Submit">
</form>
```

Form class can be defined in the `forms.py` file and ease greatly the handling of parameters received upon form submission. 
Furthermore Forms and Models are easly converted into one another in order to smooth the process of persisting data onto a db.

## Form Fields
Each form class can define a form field by defining a property for the specific class. These fields correspond to the HTML elements they eventually render on the user’s browser
- `CharField` - text input
	- `widget`
- `EmailField` - email input
- `IntegerField`
	- `min_value`
	- `max_value`
- `FloatField`
- `MultipleChoiceField`
- `FileField`
- `ChoiceField`
	- `choices` - list of tuples
- `ImageField`

## Field Arguments
Form field can be customized by passing the following arguments to shape their HTML behaviour.
- `required` each field assumes the value is required by default.
- `label` the argument helps specify a label for the field.
- `initial` initial values can be set for specific fields.
- `help-text`  specify descriptive text for the field.
- `widget` change the aspect of the input field by passing further attributes.

## Form Template

The form object thus translates to HTML script of form – minus the `<form>` as well as the `<table>` tag. To render it on a browser, youhave to first write an HTML template and put the form object in `jinja2` tag. 

```html
<!-- form.html -->
<html>
<body>
    <form action="/form" action="POST">
        {% csrf_token %}
        <table>
            {{ f }}
        </table>
</body>
<html>
```

Note: `csrf_token` is a way of saying Django that the data from this form is safe.

Let there be a following view in the app’s `views.py` file which renders the `forms.html` template and sends the ApplicationForm object as a context:
```python
# views.py
from .forms import ApplicationForm 

def index(request): 
    form = ApplicationForm() 
    return render(request, 'form.html', {'form': form})
```

Inside the template, the form can be rendered in different ways.
- `{{ form.as_table}}` - render the form field as table cells wrapped in `<tr>` tags
- `{{ form.as_p }}` - will render them wrapped in`<p>` tags.
- `{{ form.as_ul }}` - will render them wrapped in `<li>` tags.
- `{{ form.as_div }}` - will render them wrapped in `<div>` tags.

#### Validate and access received Form parameter 

`django.forms` has also a role in the reception of the parameters post within a form. In particular, Form class provides `is_valid()` that runs validation on each field and returns True if all field validation are passed.

```python
from .forms import ApplicationForm    

def index(request):  
     if request.method == 'POST': 
        form = ApplicationForm(request.POST) 
        # check whether it's valid: 
        if form.is_valid(): 
            # process the data  
            # ... 
            # ... 
            return HttpResponse('Form successfully submitted')
```

After that, still within the `views.py` form parameter can be access via `cleaned_data` attribute of the Form class.

```python
name = form.cleaned_data['name'] 
add = form.cleaned_data['address'] 
post = form.cleaned_data['posts']
```
## Models vs Forms

Django also auto-creates the form based on model definitions (it is called **ModelForm**), using field types.

1. In the `models.py` create a class and pass the argument `models.Model`
2. Define inside of this class the attribute of the model using **Form Field** type
3. Register the model in the `admin.py` with the `admin.site.register()` method
4. Create a file in the `myapp` directory called `'forms.py'` and add the following:
	-   Import the model class from the file `'models.py'`.
	-   Import the module `forms` from the package `django`
5. Import the model from `models.py` and forms from `django` module
6. Create a class passing `forms.ModelForm` as argument
7. Inside this class create a `Meta` class and define 2 attributes
	+ `model` = ModelName (define in `models.py`)
	+ `fields` = list of fields to select from above module ("`__all__`" to grab them all)
8. Make migrations
9. Implement your logic in the views.py: instanciate a new ModelForm object and `save()` after assign the attribute values to persist data.

# Admin
Django’s authorization and authentication system are provided by django.contrib.admin app, which is installed by default. It can be seen in the INSTALLED_APPS in the project’s settings.py file.

A super user has the privilege to add or modify users and groups.