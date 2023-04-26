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

The class only needs a **queryset** and a **serializer**, everything else is handle automatically.

The class only needs a queryset and a serializer, everything else is handle automatically.

```python
class MenuItemView (viewsets.ModelViewSet):
	...
```

<mark>Note</mark>: **Serializer** convert model object into python data type that can be casted into XML or JSON file. Same apply to the HTTP request where serializer help converting request body parameter into python data types.
Serializers are declared inside a the `serializers.py` in the **app folder** file and a typical declaration of it resemble the following code:

```python
# serializers.py @app level
from rest_framework import serializers
from .models import MenuItem

class MenuItemSerializer(serializers.ModelSerializer):
	class Meta:
		model = MenuItem
		fields = ['id', 'title', 'price', 'inventory']
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

## Django Debug Toolbar

This enable the tool-bar for troubleshooting and debuggin purposes.
```bash
pipenv install django-debug-toolbar
```

Add this in the `settings.py` file:

```python
# settings.py
INSTALLED_APPS = [
	'debug_toolbar',
]

MIDDLEWARE = [
	'debug_toolbar.middleware.DebugToolbarMiddleware',
]

INTERNAL_IPS = [
	'127.0.0.1',
]
```

In the urls.py devs needs to add:
```python
# urls.py
path('__debug__/', include('debug_toolbar.urls'))
```

## Serializers

Serializers make it easy for devs to implement two-way interact between Models and returned values in response to HTTP request.
Serializers are convient every time devs want to _process_ models data before sending it out. For example if we want o hide some of the field within a model can not be done we a simple view implementation like the following:

```python
# models.py
from django.db import models

class MenuItem(models.Model):
	title = models.CharField(max_length=255)
	price = models.DecimalField(max_digits=6, decimal_place=2)
	inventory = models.CharField(max_length=255)
```

```python
# views.py
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .model import MenuItem

@api_view()
def menu_items(request):
	items = MenuItem.objects.all()
	return Response(items.values())
```

Visiting the _menu_item_ endpoint will output all the menu items rendered in json format. Thanks to the DRF functionality the views automatically takes care of converting the resulting **queryset** to a JSON format.
<mark>Note</mark>: although the implementation is straight forward, customization like hiding one or more field of an object model **can't be done without the help of a serializer**.

```python
from rest_framework import serializers

class MenuItemSerializer(serializers.Serializer):
	id = serializers.IntegerField()
	title = serializers.CharField(max_length=255)
```
Serializers field are like Model field and represents the field that will be represented in th HTTP output. The resulting view function will looks like the following code:
```python
# views.py
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .model import MenuItem

#added for serialization
from .serializers import MenuItemSerializer

@api_view()
def menu_items(request):
	items = MenuItem.objects.all()
	serialized_item = MenuItemSerializer(item, many=True)
	return Response(serialized_item.data)
```
 Two important aspects
 1. import Serializer previously coded
 2. _many=True_ allow to cast a list of objects into a JSON output

This is because in this particular case the queryset returned from the model and store in the 'items' variable is a list of objects. Would the view retrived a single menu item (single object) the "many=True" would not be a requirement.
```python
# views.py
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .model import MenuItem

#added for serialization
from .serializers import MenuItemSerializer

@api_view()
def single_item(request):
	items = MenuItem.objects.get(pk=id)
	serialized_item = MenuItemSerializer(item)
	return Response(serialized_item.data)
```

### Model Serializers
Represents a more convinient way to iteract and operate on models using Serializers.
```python
from rest_framework import serializers
from .model import MenuItem

class MenuItemSerializer(serializers.Serializer):
	stock = serializers.IntegerField(source='inventory')
	price_after_tax = serializers.SerializerMethodField(method_name = 'calculate_tax')
	class Meta:
		model = MenuItem
		fields = ['id', 'title', 'price', 'stock']
		
	def calculate_tax(self, product:MenuItem):
		return product.price * Decimal(1.1)
```

- Import object model required for manipulation
- Define fields you want to output
	- Implement logic for field that not part of the original model
- Write Meta class and assign two variables **model** and **field**

## Relationship Serializers
To convert related models to JSON and output them correctly.
```python
# models.py

from django.db import models

class Category(models.Model):
	slug = models.SlugField()
	title = models.CharField(max_length=255)

	def __str__(self)-> str:
		return self.title
	
class MenuItem(models.Model):
	title = models.CharField(max_length=255)
	price = models.DecimalField(max_digits=6, decimal_places=2)
	inventory = models.SmallIntegerField()
	category = models.ForeignKey(Category, on_delete=models.PROTECT, default=1)
```

**Note**: devs need to avoid the deletion of a Category if there at least one MenuItem records that include that category.

To show data from related table we need to:
1. Specify the *field* in the `fields` list of the **Meta** class.
2.  Add an import statement that show how to fetch that piece of information
	- import related model category on top
	- declare a field variable for that data to be stored and **serialize** its content
3. 
```python
# serializers.py

from rest_framework import serializers
from decimal import Decimal
from .models import MenuItem
from .models import Category # <-------

class MenuItemSerializers(serializers.ModelSerializer):
	stock = serializers.IntegerField(source='inventory')
	price_after_tax = serializers.SerializerMethodField(method_name='calculate_tax')
	category = serializers.StringRelatedField() # <------- Require the model class to have a __str__ method defined
	
	class Meta:
		model = MenuItem
		fields = ['id','title','price','stock', 'price_after_tax','category']

	def calculate_tax(self, product:MenuItem):
		return product.price * Decimal(1.1)
```
 Adjust the previews `views.py` file with the correct sql-like syntax. In this case we are not trying to fetch all menu item object but only the ones related with a specific category.
```python
# views.py

from rest_framework.response import Response
from rest_framework.decorators import api_view
from .model import MenuItem

#added for serialization
from .serializers import MenuItemSerializer

@api_view()
def menu_items(request):
	#items = MenuItem.objects.all() <--- Replaced line
	items = MenuItem.objects.select_related('category').all()
	serialized_item = MenuItemSerializer(item, many=True)
	return Response(serialized_item.data)

@api_view()
def single_item(request):
	items = MenuItem.objects.get(pk=id)
	serialized_item = MenuItemSerializer(item)
	return Response(serialized_item.data)
```

### Nested Field
Devs can also fetch the entire **Category** object and display it within the contenxt of a MenuItem object. This require a separate serializer since we are trying to serialize not a single string but a queryset.
Therefore we can change the `serializers.py` as it follow:
```python
# serializers.py

from rest_framework import serializers
from decimal import Decimal
from .models import MenuItem
from .models import Category

class CategorySerialiazer(serializers.ModelSerializer): 
	class Meta:
		model = Category
		fields = ['id','slug','title']

class MenuItemSerializers(serializers.ModelSerializer):
	stock = serializers.IntegerField(source='inventory')
	price_after_tax = serializers.SerializerMethodField(method_name='calculate_tax')
	category = CategorySerializer() # <-------
	
	class Meta:
		model = MenuItem
		fields = ['id','title','price','stock', 'price_after_tax','category']

	def calculate_tax(self, product:MenuItem):
		return product.price * Decimal(1.1)
```

Output would looks like the following:

```json
[
	{
		"id": 1,
		"title": "Chocolate Cake",
		"price": "2.50",
		"stock": 100,
		"price_after_tax": 2.75,
		
		category: {
			"id": 1,
			"slug": "icecream",
			"title": "Icecream"
		}
	},
	{
		"id": 2,
		"title": "Vanilla Ice Cream",
		"price": "1.50",
		"stock": 100,
		"price_after_tax": 2.75,
		category: {
			"id": 2,
			"slug": "..",
			"title": ".."
		}
	},
]
```

Instead of declaring the category field as `CategorySerializer` you can specify that `depth=1` is in the **Meta** class in `MenuItemSerializer`. This way, all relationships in this serializer will display every field related to that model.  You can change the code of the `MenuItemSerializer` as below.
```python
class MenuItemSerializer(serializers.ModelSerializer):
    stock =  serializers.IntegerField(source='inventory')
    price_after_tax = serializers.SerializerMethodField(method_name = 'calculate_tax')
    # category = CategorySerializer()

    class Meta:
        model = MenuItem
        fields = ['id','title','price','stock', 'price_after_tax','category']
        depth = 1 # <----- This way, all relationships in this serializer will display every field related to that model

    def calculate_tax(self, product:MenuItem):
        return product.price * Decimal(1.1)
```


### Display a related model fields field as a hyperlink

In DRF you can display every related model field as a hyperlink in the API output. Like this: http://127.0.0.1:8000/api/category/{categoryId} for the category field. There are two different ways to do this. The first method is to use the serializer field called HyperlinkedRelatedField and for the second method you use the HyperlinkedModelSerializer.

**Method 1: HyperlinkedRelatedField**

**Step 1: Create and map a new view function**

Every HyperlinkedRelatedField field in a serializer needs a queryset to find the related object and a view name that is used to map the hyperlinked URL pattern.

Thus you have to create a new function in the views.py file that will handle the categoryId endpoints.
```python
from .models import Category from .serializers import CategorySerializer

@api_view()
def category_detail(request, pk):
    category = get_object_or_404(Category,pk=pk)
    serialized_category = CategorySerializer(category)
    return Response(serialized_category.data) 
```
Then you map this function in the **urls.py** file with a view name.

```python
path('category/<int:pk>',views.category_detail, name='category-detail')
```

_**Tip:**_ _There is a convention you must follow when you create this view name. The rule is that you have to add_ -detail _after the related field name, which is_ category _in the_ MenuItemSerializer_. This is why the view name was_ category-detail _in this code. If the related field name was_ user_, the view name would be_ user-detail_._

**Step 2: Create a HyperLinkedRelatedField in the serializer**

The next step is to change the MenuItemSerializer code. The following code sets the category field as a HyperLinkedRelatedField in the MenuItem serializer.
```python
from .models import Category

class MenuItemSerializer(serializers.ModelSerializer):
    stock =  serializers.IntegerField(source='inventory')
    price_after_tax = serializers.SerializerMethodField(method_name = 'calculate_tax')

    category = serializers.HyperlinkedRelatedField(
        queryset = Category.objects.all(),
        view_name='category-detail'
    )

    class Meta:
        model = MenuItem
        fields = ['id','title','price','stock', 'price_after_tax','category']    

    def calculate_tax(self, product:MenuItem):
        return product.price * Decimal(1.1)
```
Note how a queryset and a view name are provided in the category HyperlinkedRelatedField. The code follows the convention so you can remove the line, view_name='category-detail. It is only necessary if you didn’t follow the convention and you created the view name in a different way in the **urls.py** file.

**Step 3: Add context**

The final step is to add context to the MenuItemSerializer in the menu_items function, as below.

```python
serialized_item = MenuItemSerializer(items, many=True, context={'request': request})
```
**Note**: _The argument_ context={'request': request} _lets the_ menu-items _endpoint display the category field as a hyperlink._

## Deserialization and validation
Opposite process of serialization and it happens when the client sends some data to your api endpoints and DRF maps those data to an existing model.

```python
# views.py
from django.shortcuts import get_object_or_404
from rest_framework.response import Response
from rest_framework.decorators import api_view
from .model import MenuItem
from .serializers import MenuItemSerializer

#added for serialization
from .serializers import MenuItemSerializer

@api_view(['GET', 'POST'])
def menu_items(request):
	if request.method == 'GET':
		items = MenuItem.objects.select_related('category').all()
		serialized_item = MenuItemSerializer(item, many=True)
		return Response(serialized_item.data)
	if request.method == 'POST:
		serialized_item = MenuItemSerializer(data=request.data)
		serialized_item.is_valid(raise_exception=True) #<---- Validate data sent from client
		serialized_item.save() #<--- Has to save this data brefore being able to access it
		return Response(serialized_item.data, status.HTTP_201_CREATED) 
	
@api_view()
def single_item(request, id):
	items = get_object_or_404(MenuItem, pk=id)
	serialized_item = MenuItemSerializer(item)
	return Response(serialized_item.data)
```