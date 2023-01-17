
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
 