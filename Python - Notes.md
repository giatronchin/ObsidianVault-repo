
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

---------------------------------------------------------------------------------------
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

---------------------------------------------------------------------------------------
Using range():  {0: 0, 1: 2, 2: 4, 3: 6, 4: 8, 5: 10, 6: 12, 7: 14, 8: 16, 9: 18, 10: 20, 11: 22}

Using one input list to create dict:  {1: 1, 2: 4, 3: 9, 4: 16, 5: 25, 6: 36, 7: 49, 8: 64, 9: 81, 10: 100, 11: 121, 12: 144}

Using two lists:  {1: 'Jan', 2: 'Feb', 3: 'Mar', 4: 'Apr', 5: 'May', 6: 'June', 7: 'July', 8: 'Aug', 9: 'Sept', 10: 'Oct', 11: 'Nov', 12: 'Dec'}

```
Here it has been used the `zip` function that **combines the two lists**. When the two lists are of unequal length, the length of the shorter list is the length of the dictionary.

## Set Comprehension
Same as list-comprehension except the use of { } instead of [ ].
