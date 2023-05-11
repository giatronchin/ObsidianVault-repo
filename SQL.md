# Common SQL Commands

#sql #database #cli

The objective of this reading is to teach you how to name and explain the main commands in SQL. SQL is the most widely used database query language. It is designed for retrieving and managing data in a relational database. SQL can be used to perform different types of operations in the database such as accessing data, describing data, manipulating data and setting users roles and privileges (permissions). 

Here you will learn about the main commands used in SQL. At a later stage you will explore relevant examples of how to use these commands with a detailed explanation of the SQL syntax for key operations such as to create, insert, update and delete data in the database. 

The SQL Commands are grouped into four categories known as DDL, DML, DCL and TCL depending on their functionality, namely the type of operation they’re used to perform.  Let’s explore these commands in greater detail.

## **Data Definition Language (DDL)**

The SQL DDL category provides commands for defining, deleting and modifying tables in a database. Use the following commands in this category.

**CREATE Command**

Purpose: To create the database or tables inside the database

Syntax to create a table with three columns:

`CREATE TABLE table_name (column_name1 datatype(size), column_name2 datatype(size), column_name3 datatype(size));`

**DROP Command**

Purpose: To delete a database or a table inside the database. 

Syntax to drop a table:

`DROP TABLE table_name;` 

**ALTER Command**

Purpose: To change the structure of the tables in the database such as changing the name of a table, adding a primary key to a table, or adding or deleting a column in a table.

1.  Syntax to add a column into a table:

`ALTER TABLE table_name ADD (column_name datatype(size));`

2. Syntax to add a primary key to a table:

`ALTER TABLE table_name ADD primary key (column_name);`

**TRUNCATE Command**

Purpose: To remove all records from a table, which will empty the table but not delete the table itself. 

Syntax to truncate a table:

`TRUNCATE TABLE table_name;`

**COMMENT Command**

Purpose: To add comments to explain or document SQL statements by using double dash (--) at the start of the line. Any text after the double dash will not be executed as part of the SQL statement. These comments are not there to build the database. They are only for your own use.   

Syntax to COMMENT a line in SQL: 

`--Retrieve all data from a table SELECT * FROM table_name;`

## **Data Manipulation Language (DML)**

The SQL DML commands provide the ability to query, delete and update data in the database.  Use the following commands in this category.

**SELECT Command**

Purpose: To retrieve data from tables in the database. 

Syntax to select data from a table:

`SELECT * FROM table_name;`

**INSERT Command**

Purpose: To add records of data into an existing table. Syntax to insert data into three columns in a table:

`INSERT INTO table_name (column1, column2, column3) VALUES (value1, value2, value3);`

Otherwise INSERT INTO also accept output from SELECT statement as input value:
`INSERT INTO table_name SELECT * FROM source_table`


**UPDATE Command**

Purpose: To modify or update data contained within a table in the database. 

Syntax to update data in two columns:

`UPDATE table_name SET column1 = value1, column2 = value2 WHERE condition;`

**DELETE Command**

Purpose: To delete data from a table in the database.

Syntax to delete data:

`DELETE FROM table_name WHERE condition;`

## **Data Control Language (DCL)**

You use DCL to deal with the rights and permissions of users of a database system. You can execute SQL commands to perform different types of operations such as create and drop tables. To do this, you need to have user rights set up. This is called user privileges. This category deals with advanced functions or operations in the database. Note that this category can have a generic description of the two main commands. Use the following commands in this category:

**GRANT** Command to provide the user of the database with the privileges required to allow users to access and manipulate the database.

**REVOKE** Command to remove permissions from any user.

## **Transaction Control Language (TCL)** 

The TCL commands are used to manage transactions in the database. These are used to manage the changes made to the data in a table by utilizing the DML commands. It also allows SQL statements to be grouped together into logical transactions. This category deals with advanced functions or operations in a database. Note that this category can have a generic description of the two main commands. Use the following commands in this category:

**COMMIT** Command to save all the work you have already done in the database. 

**ROLLBACK** Command to restore a database to the last committed state.

------------------------------------------

To lookup for **MySQL** syntax please refer the following link([[MySQL]])

------------------------------------------

# Relational model

You are already familiar with table relationships, one of the main concepts behind the relational database model. In this reading, you’ll find out more about the relational database model. Though it was introduced a long time ago, it is still the most widely used data model for commercial databases. 

## **What is the relational model?**

The relational model is built around three main concepts which are: 
-   Data  
-   Relationships
-   Constraints

It describes a database as “a collection of inter-related relations (or tables)”. It is still a dominant model used for data storage and retrieval. In essence, it is a way of organizing or storing data in a database. SQL is the language that’s used to retrieve data from a relational database.

## **Fundamental concepts of the relational model**

### **Relation**

A relation represents a file that stores data. It’s also known as a table. Within a table there are rows and columns. Each row represents a group of related data values. A row, or record, is also known as a tuple. Columns in a table are also known as fields or attributes. These columns define or describe a row. Therefore, a record or a row consists of a set of attributes.

![Example of a relational model](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/MIctKk-5Q8yHLSpPuUPMhQ_a36cc4efaa244ba8888b4f339ad4e6a1_C1M4L2Item02Image01.png?expiry=1674691200000&hmac=LqugaaensTGmxN1MZvTxDQOfBwK49dQwlw_SdVri7Hw)

### **Column**

A table stores pieces of data or facts as columns. In other words, the principal storage unit of a database is a column (attribute). When determining the columns for your table, think about the pieces of data that need to be stored within that table. Each column is a generic representation of the piece of data that needs to be stored. Each table cell that becomes a part of a row will have a specific instance of a piece of data.

|ID| First Name| Last Name|
| --- | --- | --- |
|1|John|Smith|
|2|Mish|Azerrad|
|3|Peter|Klien|

For example, ID, First Name and Last Name are columns. The ID numbers, first names and last names are instances or pieces of data that are stored in those columns. Also, every column has a specific data type, which could be numeric, text, or date.

## **Domain**

The domain is a set of acceptable values that a column is allowed to contain. The domain depends on the data type of the column. Namely whether it is numeric, or text based. The domain of ID has a set of acceptable and possible values that are numeric such as1, 2 and 3. The domain of First Name has a set of acceptable and possible values that are text based, which is people’s first names. In the ID column, it’s not possible to store values such as “John” or “001”. Similarly, the First Name column can’t accept any numeric pieces of data.

## **Record or tuple**

A record, also known as a tuple, is a row within a table. If a table has columns for ID, First Name and Last Name, then one record or tuple would have one person's ID, first name and last name. Another record would have another person’s full personal information.

## **Key**

Each row or tuple has one or more attributes, known as a relation key, that can uniquely identify a specific row. This is also known as the primary key.

## **Degree**

Degree is the number of columns or attributes within a relation. A student table that stores the student's name, address, phone number and email address would have a degree of four.

## **Cardinality**

Cardinality refers to how many records there are within a particular table in a database. If you have 100 students in your student table, with all their information organized into individual rows, then that table has a cardinality of 100.

## **What are constraints?**

In the relational model, every relation needs to meet three conditions. These three conditions must be met for a relation to be valid. They are called relational integrity constraints and they are: 

- Key constraints 
- Domain constraints 
- Referential integrity constraints 

## **Key constraints**

The key constraint revolves around the key attribute(s). In the relational model, a key attribute is an identifier that can be used to refer to a record. It must also be unique for each record. For example, you can use the Student ID in the student table as the key. This means that there can’t be two students with the same Student ID. If so, it would be invalid and cause an issue when it comes to accessing or retrieving the data. Also, a key attribute cannot have NULL values. This is the requirement that should be met by the Key constraint.

## **Domain constraints**

Domain constraints are all about the requirement of inserting values that have a valid data type. There are a variety of data types that can be included within a table, namely numeric, text and data, in the case of the example. If an attempt is made to store an incorrect data typed value to an attribute, it’s declared a violation of domain constraints. For instance, if an attribute requires a numeric value to be entered, and the value you are attempting to enter uses letters instead of numbers, then it would be invalid.

## **Referential integrity constraints**

A database has multiple tables that refer to one another. Referential integrity constraints are based on the concept of foreign keys. A foreign key is a key attribute present in a table, which is also a primary key of another table to which it needs to be linked. Through this key, it references the other table to which it’s related. For example, the order ID is present in the Order_Item table as a foreign key, which is also the primary key of the order table. So, the order table and the Order_Item table are related to each other because the Order_Item table references the order table via the order ID attribute. The referential integrity constraint states that if a relation refers to a key attribute of another relation, then that key element must exist. In other words, there must be matching values in the two tables for that attribute.

## **Types of relationships**

In the relational model, there are three types of relationships that can exist between tables.

- One-to-one 
- One-to-many 
- Many-to-many 

## **One-to-one**

In order to understand one-to-one relationships, let’s take the example of two tables: Table A and Table B. A one-to-one (1:1) relationship means that each record in Table A relates to one, and only one, record in Table B. Likewise, each record in Table B relates to one, and only one, record in Table A. 

Here is a diagram that illustrates the example:

![Example of a one-to-one relationship](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/sLi6VnmzQim4ulZ5swIp_A_1649a66b4515471f9515c41377344fa1_C1M4L2Item02Image03.png?expiry=1674691200000&hmac=XAd15vNiWWm-2YihTINf1k7OiGygms1PP0lK3QCa0Y8)

Here, every country has one, and only one, capital. And every capital belongs to one and only one country.

## **One-to-many**

If there are two tables, Table A and Table B, a one-to-many (1:N) relationship means a record in Table A can relate to zero, one, or many records in Table B. Many records in Table B can relate to one record in Table A. 

Let’s examine the following relationship between customers and orders.

![Example of a one-to-many relationship](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/4hA7IDZUTDWQOyA2VLw19A_fc624a7505ff46e0a848bffc74692fa1_C1M4L2Item02Image04.png?expiry=1674691200000&hmac=zo_ICAsjdgBAE-X_km5NArJGzguDOYEuzW2vk1lMKjw)

Here, each customer can place many orders. Many records in the order table can relate to only one record in the customer table. 

## **Many-to-many**

If there are two tables, Table A and Table B, a many-to-many (N:N) relationship means many records in Table A can relate to many records in Table B. And many records in Table B can relate to many records in Table A. Let’s examine this example of many-to-many relationship between customer and product with the use of a diagram. In the diagram, customers can purchase various products, and products can be purchased by many customers.

![Example of a many-to-many relationship.](https://d3c33hcgiwev3.cloudfront.net/imageAssetProxy.v1/EmdnDzmvROWnZw85r5Tlng_f95e3dcb45d24cdabe512dee329da5a1_C1M4L2Item02Image05.png?expiry=1674691200000&hmac=_0YzHGQ-YRVGEymQaRnnUlka7kdnElU3qZne5hCJNiM)

Usually, many-to-many relationships are not kept in a data model. They are broken down into two one-to-many relationships by introducing a junction or middle table.

To conclude, there are many benefits to the relational database model. This includes the ability to design and develop a meaningful system of information, and the ability to access and retrieve every single piece of data stored in the database.