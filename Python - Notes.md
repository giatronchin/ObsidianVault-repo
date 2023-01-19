
# File handling
```python
open(<FILE_NAME> | <FILE_LOCATION>, <MODE>)
```
Mode arguments is used to address the interaction mode with the file _accessing_, _reading_, _creating_ **or** if the file is **text**/**binary**:
-  `r` to open and read file in text format
-  `rb` to open and read file in binary format
-  `r+` to open for reading and writing
-  `w` to open for writing (overwrite current content of the file)
-  `a` to append to a file

Once done with the file manipulation we should always close the file with the command:

```python
close()
```
One more way to close the file automatically:
```python
with open ('filename.txt', r) as f:
	something#
```

Example:
```python
file = open('test.txt', mode = 'r')

data = file.readline() #first line of the document

data = file.readlines() #return arra  with elements each line  of the documents

file.close()
```
Example2:
```python
with open('test.txt', mode = 'r') as file:
	
	data = file.readline() #first line of the document
	
	data = file.readlines() #return arra  with elements each line  of the documents
```
Example3 - Create a new file (overwritte whatever was the content if the file already existed):
```python
with open('newtest.txt', mode = 'w') as file:
	file.write("Something on one line")
	file.writelines(["first line","\nsecond line"]) #accept a list of string to write on multiple line
```

Example4 - Append new line to an existing file:
```python

try:
	with open('newtest.txt','a') as file:
		file.writelines(["new line"]) 
except FileNonFoundError as e:
	print('ERROR', e)

```

### Reading files

File object allow three different methods:
- `read` - return content of the whole file a string containing all characters. Can specify an argument `int` to target the first `n` characters
- `readline` - return the first line a string. Also here we can specify the argument `n` to target the first n-character
- `readlines` - return array of string each of them containing a single line


## Functional Programming
#functional
#### Map & Filter

**Map** take all objects in a list and applies a function to each and every element.
Create a new list containing the element of 'menu' that begin with letter 'c'.
```python
menu = ['espresso','mocha', 'latte', 'cappuccino', 'cortado', 'americano']

def find_coffee(coffee):
	if coffee[0] == 'c':
		return coffee

map_coffee = map(find_coffee, menu)
print(map_coffee)

for x in map_coffee:
	print(x)

```

Same implementation like above but with `filter` function.
**Filter** do the same as the _map_, but take the results and creates a new list with only the **true values**.
```python
menu = ['espresso','mocha', 'latte', 'cappuccino', 'cortado', 'americano']

def find_coffee(coffee):
	if coffee[0] == 'c':
		return coffee

filter_coffee = filter(find_coffee, menu)
print(filter_coffee)
for x in filter_coffee:
	print(x)
```

# Comprehensions

Comprehensions in Python are a way to create a new sequence from an already existing sequence.

There are four main types of comprehensions in Python:
-   List comprehension   
-   Dictionary comprehension
-   Set comprehension
-   Generator comprehension



## List comprehension

The syntax for list comprehension is:

`[ <expression> for x in <sequence> if <condition>]`

The same can be illustrated with the example below.
```python
data = [2,3,5,7,11,13,17,19,23,29,31]

# Ex1: List comprehension: updating the same list
data = [x+3 for x in data]
print("Updating the list: ", data)

# Ex2: List comprehension: creating a different list with updated values
new_data = [x*2 for x in data]
print("Creating new list: ", new_data)

# Ex3: With an if-condition: Multiples of four:
fourx = [x for x in new_data if x%4 == 0 ]
print("Divisible by four", fourx)

# Ex4: Alternatively, we can update the list with the if condition as well
fourxsub = [x-1 for x in new_data if x%4 == 0 ]
print("Divisible by four minus one: ", fourxsub)

# Ex5: Using range function:
nines = [x for x in range(100) if x%9 == 0]
print("Nines: ", nines)

---------------------------------------------------------------------------------
Updating the list:  [5, 6, 8, 10, 14, 16, 20, 22, 26, 32, 34]
Creating new list:  [10, 12, 16, 20, 28, 32, 40, 44, 52, 64, 68]
Divisible by four [12, 16, 20, 28, 32, 40, 44, 52, 64, 68]
Divisible by four minus one:  [11, 15, 19, 27, 31, 39, 43, 51, 63, 67]
Nines:  [0, 9, 18, 27, 36, 45, 54, 63, 72, 81, 90, 99]
```

## Dictionary comprehension

The syntax for dictionary comprehension is:
`dict = { key:value for key, value in <sequence> if <condition> }`

Following some usage examples:
```python
# Using range() function and no input list
usingrange = {x:x*2 for x in range(12)}
print("Using range(): ",usingrange)

# Lists
months = ["Jan", "Feb", "Mar", "Apr", "May", "June", "July", "Aug", "Sept", "Oct", "Nov", "Dec"]
number = [1,2,3,4,5,6,7,8,9,10,11,12]

# Using one input list
numdict = {x:x**2 for x in number}
print("Using one input list to create dict: ", numdict)

# Using two input lists
months_dict = {key:value for (key, value) in zip(number, months)}
print("Using two lists: ", months_dict)

---------------------------------------------------------------------------------
Using range():  {0: 0, 1: 2, 2: 4, 3: 6, 4: 8, 5: 10, 6: 12, 7: 14, 8: 16, 9: 18, 10: 20, 11: 22}

Using one input list to create dict:  {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100, 11: 121, 12: 144}

Using two lists:  {1: 'Jan', 2: 'Feb', 3: 'Mar', 4: 'Apr', 5: 'May', 6: 'June', 7: 'July', 8: 'Aug', 9: 'Sept', 10: 'Oct', 11: 'Nov', 12: 'Dec'}
```
Here it has been used the `zip` function that **combines the two lists**. When the two lists are of unequal length, the length of the shorter list is the length of the dictionary.

## Set Comprehension
Same as list-comprehension except the use of { } instead of [ ].

## Generator comprehension

Generator comprehensions are also very similar to lists with the variation of using curved brackets instead of square brackets. They are also more memory efficient as compared to list comprehensions. For example:
```python
data = [2,3,5,7,11,13,17,19,23,29,31]

gen_obj = (x for x in data)

print(gen_obj)

print(type(gen_obj))

for items in gen_obj:

print(items, end = " ")
```
In the code above, I created a generator object of the class generator instead of a list. The elements in this iterator object cannot be directly accessed and need the help of a for loop and as such, I iterate over these elements and print them.


# OOP Principles

This reading introduces you to the OOP principles in more detail using some examples.

The object oriented paradigm was introduced in the 1960s by Alan Kay. At the time, the paradigm was not the best computing solution given the small scalability of software developed then. As the complexity of software and real-life applications improved, object oriented principles became a better solution.

You previously encountered the four main pillars of object oriented programming. These are: encapsulation, polymorphism, inheritance and abstraction. Let's look at a few examples that demonstrate how these principles translate when using Python.

## Encapsulation

The idea of encapsulation is to have methods and variables within the bounds of a given unit. In the case of Python, this unit is called a class. And the members of a class become locally bound to that class. These concepts are better understood with scope, such as global scope (which in simple terms is the files I am working with), and local scope (which refers to the method and variables that are 'local' to a class). Encapsulation thus helps in establishing these scopes to some extent.

For example, the Little Lemon company may have different departments such as inventory, marketing and accounts. And you may be required to deal with the data and operations for each of them separately. Classes and objects help in encapsulating and in turn restrict the different functionalities.

Encapsulation is also used for hiding data and its internal representation. The term for this is information hiding. Python has a way to deal with it, but it is better implemented in other programming languages such as Java and C++. Access modifiers represented by keywords such as public, private and protected are used for information hiding. The use of single and double underscores for this purpose in Python is a substitute for this practice. For example, let's examine an example of protected members in Python.

```python
class Alpha:

def __init__(self):

    self._a = 2.  # Protected member ‘a’

    self.__b = 2.  # Private member ‘b’
```
self._a is a protected member and can be accessed by the class and its subclasses.

Private members in Python are conventionally used with preceding double underscores: __. self.__b is a private member of the class Alpha and can only be accessed from within the class Alpha.

It should be noted that these private and protected members can still be accessed from outside of the class by using public methods to access them or by a practice known as name mangling. Name mangling is the use of two leading underscores and one trailing underscore, for example:

`_class__identifier`

Class is the name of the class and identifier is the data member that I want to access.

## Polymorphism

Polymorphism refers to something that can have many forms. In this case, a given object. Remember that everything in Python is inherently an object, so when I talk about polymorphism, it can be an operator, method or any object of some class. I can illustrate the case for polymorphism using built-in functions and operations, for example:
```python
string = "poly"

num = 7

sequence = [1,2,3]

new_str = string * 3

new_num = 7 * 3

new_sequence = sequence * 3

print(new_str, new_num, new_sequence)
```
The output is:

`polypolypoly 21 [1, 2, 3, 1, 2, 3, 1, 2, 3]`

In the example, I have used the same operator () to perform on a string, integer and a list. You can see the () operator behaves differently in all three cases.

Let's examine one more example.
```python
string = "poly"

sequence = [1,2,3]

print(len(string))

print(len(sequence))
```
The output is:
```python
4

3
```

The len() function is able to take variable inputs. In the example above it is a string and a list that provides the output in integer format.

## Inheritance

Inheritance in Python will be covered later in the course, but the basic template for it is as follows:
```python
class Parent:

    Members of the parent class

class Child(Parent):

    Inherited members from parent class

    Additional members of the child class
```
As the structure of inheritance gets more complicated, Python adheres to something called the Method Resolution Order (MRO) that determines the flow of execution. MRO is a set of rules, or an algorithm, that Python uses to implement monotonicity, which refers to the order or sequence in which the interpreter will look for the variables and functions to implement. This also helps in determining the scope of the different members of the given class.

### Abstraction

Abstraction can be seen both as a means for hiding important information as well as unnecessary information in a block of code. The core of abstraction in Python is the implementation of something called abstract classes and methods, which can be implemented by inheriting from something called the abc module. "abc" here stands for abstract base class. It is first imported and then used as a parent class for some class that becomes an abstract class. Its simplest implementation can be done as below.
```python
from abc import ABC,   

class ClassName(ABC):

    pass
```

### Class 
Some examples of syntax for the definition/declaration/initialization of a class.
```python
class MyClass():
	#pass: keywords allows the program to continue execution without impacting any functionality or flow
	a = 5
	print("Hi Class")

	def sayMorning(self):
		print("Good Morning")

my_class = MyClass()
print(MyClass.a)
my_class.sayMorning()
```
Some example of how interaction with class and instances works
```python
class House:

'''
This is a stub for a class representing a house that can be used to create objects and evaluate different metrics that we may require in constructing it.
'''

	num_rooms = 5
	bathrooms = 2
	
	def cost_evaluation(self):
		print(self.num_rooms)
		pass

# Functionality to calculate the costs from the area of the house

#Class invocation
house = House()

print(house.num_rooms) #Output: 5
print(House.num_rooms) #Output: 5

#Changing instance class attribute affect only that particular instance
house.num_rooms = 7

print(house.num_rooms) #Output: 7
print(House.num_rooms) #Output: 5

#Changing class attribute affect both class and instance initialized from it
House.num_rooms = 8

print(house.num_rooms) #Output: 8
print(House.num_rooms) #Output: 8
```

**Note** the use of the keywork _self_ in this example. self is a convention in Python, and you may use any other word in its place, but as a practice, it is easy to recognize.

`__init__` it's a method (also known as constructor) that state what action are performed during initializatin of an instance object.