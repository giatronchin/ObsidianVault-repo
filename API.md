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
## Routing in DRF

### Regular Routing
The code below maps a function from a views.py file to an API endpoint.

```python
from django.urls import path
from . import views
urlpatterns = [
	path('books’,views.books) ,
]
```

As long as the function in the views.py file has the api-decorator  everything works as seen before.
```python
# views.py
@api_view([‘GET’,’POST’])
```


### Routing to a class method
Devs can also map a specific method from a class. To do this they need to declare that method as a `@staticmethod` first. After that, you can map it in the `urls.py` file. 

```python
# views.py
class Orders():

	@staticmethod
	@api_view()
	def listOrders(request):
    	return Response({'message':'list of orders'}, 200)
```
The mapping is completed by listing the change in the urls.py at the app level.
```python
from django.urls import path
from . import views
urlpatterns = [
	path('orders', views.Orders.listOrders)
]
```

### Routing class-based views
One can save a lot of time in DRF by mapping a class that extends the **APIview**. When a class extends APIview or generic views, you can simply map those classes in the urls.py file.

```python
# views.py
class BookView(APIView):
	def get(self, request, pk):
    	return Response({"message":"single book with id " + str(pk)}, status.HTTP_200_OK)
	def put(self, request, pk):
    	return Response({"title":request.data.get('title')}, status.HTTP_200_OK)
```

```python
# urls.py @app level
from django.urls import path
from . import views
urlpatterns = [
    path('books/<int:pk>',views.BookView.as_view())
]
```
 If the class has post(), delete() and patch() methods, it will work with HTTP POST, DELETE and PATCH methods too.

 ### Routing classes that extend viewsets
The routing toward classes can go deeper as a class can extend different types of `ViewSets`. Different methods within a child class of ViewSet can be mapped to different urls and http request method.

```python
# views.py
class BookView(viewsets.ViewSet):
	def list(self, request):
    	return Response({"message":"All books"}, status.HTTP_200_OK)
	def create(self, request):
    	return Response({"message":"Creating a book"}, status.HTTP_201_CREATED)
	def update(self, request, pk=None):
    	return Response({"message":"Updating a book"}, status.HTTP_200_OK)
	def retrieve(self, request, pk=None):
    	return Response({"message":"Displaying a book"}, status.HTTP_200_OK)
	def partial_update(self, request, pk=None):
        return Response({"message":"Partially updating a book"}, status.HTTP_200_OK)
	def destroy(self, request, pk=None):
    	return Response({"message":"Deleting a book"}, status.HTTP_200_OK)
```
Notice how BookView now inherit from the `viewsets.ViewSet` class instead of `APIView`.
```python
# urls.py @app level
urlpatterns = [
	path('books', views.BookView.as_view(
    	{
        	'get': 'list',
        	'post': 'create',
    	})
	),
    path('books/<int:pk>',views.BookView.as_view(
    	{
        	'get': 'retrieve',
        	'put': 'update',
        	'patch': 'partial_update',
        	'delete': 'destroy',
    	})
	)
]
```
Url Mapping can be much more flexible thanks to this kind of implementation. Notice how the <mark>HTTP verbs</mark> are mapped with **each method in this class**. Also, note that both the `list()` and `retrieve()` methods are present, although they point both a common http request method: get. The `list()` method is used to **display all books**, and the `retrieve()` method is used to **display a single book**.

### Routing with SimpleRouter class in DRF
Class that extends ViewSets can use different types of built-in routers to map those classes in your urls.py file. Doing things this way means you don’t have to map the individual methods as you did in the previous section. Initiate a SimpleRouter object and map the class in the urls.py file in your Django app as follows.
```python
# urls.py @app level
from rest_framework.routers import SimpleRouter
router = SimpleRouter(trailing_slash=False)
router.register('books', views.BookView, basename='books')
urlpatterns = router.urls
```
Note: Without the argument `trailing_slash=False`, the API endpoints will have a trailing slash. 

### Routing with DefaultRouter class in DRF
Similar to the SimpleRouter object, it creates an API root endpoint with a trailing slash that displays all your API endpoints in one place (sort of docs-api view of all available endpoints).
```python
# urls.py @app level
from rest_framework.routers import DefaultRouter
router = DefaultRouter(trailing_slash=False)
router.register('books', views.BookView, basename='books')
urlpatterns = router.urls
```

## ViewSets
There are different types of classes that belongs to the ViewSets family. All of them trying to speed up API implementation for the common user.

To use these classes, you must import the viewsets module from the rest_framework:  `from rest_framework import viewsets`.

### 1.ViewSet
It does extend APIView class bringing browsable API views out of the box. it also provide common methods to address most frequent API use case. To  extend a a ViewSet class: 

`class MenuItemViewSet (viewsets.ViewSet)`

- If the API endpoint has to deal with a <mark>collection of resources</mark>

| Class Method | Supported HTTP Method | Purpose |
| --- | --- | --- |
| `list()` | **GET** | Display resource collection |
| `create()` | **POST** | Create new resource |

- If the API endpoint has to deal with a <mark>single resource</mark>

| Class Method | Supported HTTP Method | Purpose |
| --- | --- | --- |
| `retrive()` | **GET** | Display resource collection |
| `update()` | **PUT** | Create new resource |
| `partial_update()` | **PATCH** | Display resource collection |
| `destroy()` | **DELETE** | Create new resource |

### 2.ModelViewSet
To enable developers to code class-based view that can handle automatically CRUD operations they can extend the ModelViewSet class.
The class only needs a queryset and a serializer, everything else is handle automatically.
```python
class MenuItemView (viewsets.ModelViewSet):
	...
```
### 3.ReadOnlyModelViewSet
An extension on the same theme is the `ReadOnlyModelViewSet` that as the name suggest will **only allow display** of a single resource or a collection of resources.
No `POST`, `PUT`, `PATCH`, `DELETE` methods are allowed on this ViewSet.

```python
class ReadOnlyMenuItemView(viewsets.ReadonlyModelViewSet):
	...
```

## Generic View
Those are ViewSet that facilitate the coding of the simple functionality of the API endpoint. To be able to use those devs have to import `from rest_framework import generics`. The view map specific use-case with multiple View class devs could inherit from. Mixed use-case are allow as long as we extend multiple class.

|Generic view class|Supported method|Purpose|
| --- | --- | --- |
| **CreateAPIView** | POST | Create a new resource|
| **ListAPIView** | GET | Display resource collection|
| **RetrieveAPIView** | GET | Display a single resource|
| **DestroyAPIView** | DELETE | Delete a single resource|
| **UpdateAPIView** | PUT and PATCH | Replace or partially update a single resource|
| **ListCreateAPIView** | GET, POST | Display resource collection and create a new resource|
| **RetrieveUpdateAPIView** | GET, PUT, PATCH | Display a single resource and replace or partially update it|
| **RetrieveDestroyAPIView** | GET, DELETE | Display a single resource and delete it|
| **RetrieveUpdateDestroyAPIView** | GET, PUT, PATCH, DELETE | Display, replace or update and delete a single resource|

**Example**: API endpoints capable of displaying resource collection and creating a new resource, have to extend both `ListAPIView` and `CreateAPIView`, or just `ListCreateAPIView`.
```python
class MenuItemView (generics.ListAPIView, generics.CreateAPIView)
```

Just like ModelViewSet, you must give these generic view classes a queryset and a serializer and you don’t need to manually write code to perform these database operations. 

## Authentication & Selective Authentication
If we want **all API calls** to be **authenticated** in a class-based view that extends the generic views, you can add the `permission_classes` public **attribute** in the class.

```python
permission_classes = [IsAuthenticated]
```

In case authentication is required only for group of request methods we have to override the methods permission. Let's say we  want to selectively enable authentication for some calls, like POST, PUT, PATCH and DELETE then you need to override the `get_permission` method in your class-based view like this.

```python
def get_permissions(self):
        permission_classes = []
        if self.request.method != 'GET':
            permission_classes = [IsAuthenticated]
            
        return [permission() for permission in permission_classes]

```
Sometimes in a class-based view that extends a generic view, you may want to return resources created by the authenticated users only. In that case, you need to override the `get_queryset` method. 

```python
class OrderView(generics.ListCreateAPIView):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer
    permission_classes = [IsAuthenticated]
    def get_queryset(self):
        return Order.objects.all().filter(user=self.request.user)
```

Eventually although ViewSet aimed to ease the API implementation, devs can achive customization overriding the related DRF method (http-verbs): `get()`, `post()`, `put()`, `patch()`, `delete()`.

```python
class OrderView(generics.ListCreateAPIView):
    queryset = Order.objects.all()
    serializer_class = OrderSerializer  
    def get(self, request, *args, **kwargs):
        return Response(‘new response’)
```
