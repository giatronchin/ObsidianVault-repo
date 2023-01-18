
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


```python



```