#mysql #linux #database

Installation server package
```bash
sudo apt update
sudo apt install mysql-server
```

Secure configuration, remote test tables, _set strong password for root_, _disable remote login for root_, flush all priveledges
```bash
sudo mysql_secure_installation  
```

Startup my-sql-cli
```bash
sudo mysql
```

Create a personal user:
```mysql
CREATE USER 'mysqluser'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
    
GRANT ALL ON *.* TO 'mysqluser'@'localhost';
```

Instead of localhost use '%' to connect from anywhere. Also you will need to edit `bind.address` config to allow external connection:
```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf 
```

Connection syntax
```bash
sudo mysql -u username -h db.host name -P port# -p password
```
```mysql
show databases;
show tables;

create database dbname;
   
use dbname; #now on u are interacting with that db
create table tableName (col1Name type, col2Name varchar);    
   
describe tableName; #show schema of the table
alter table tableName action(add/..) newCol type;
update tableName set colName = Null where condition2; #update record in the table
   
delete from tableName where condition1;

insert into tableName values (1, “blabla”);
insert into tableName (col1Name, Col3Name) values (2, "bliti"); #to insert a new record only fill out 2 of 3 column available
   
select * from tableName where condition1 or/and condition2;
   
select * from tableName where col1Name = "Something";
   
select * from tableName where col1Name like "a%"; #Only records that has col1Name value that start with "a". "%a" would mean "ends with a".
```

### Comparison Operators

```mysql
SELECT * FROM employee WHERE salary = 18000;

SELECT * FROM employee WHERE salary > 18000;
SELECT * FROM employee WHERE salary < 18000;

SELECT * FROM employee WHERE salary >= 18000;
SELECT * FROM employee WHERE salary <= 18000;

SELECT * FROM employee WHERE salary <> 18000; --Not equal to
SELECT * FROM employee WHERE salary != 18000; --Not equal to
```

### Sorting & Filtering

Order output of the query with the **order by** clause:
```mysql
SELECT *
FROM tab
ORDER BY column_name ASC | DESC, column_name2, ... , column_nameN; #Ascending or descending order
```

Filter the output of the query using the **where** clause:
```mysql
SELECT *
FROM tab
WHERE condit; #column_name  = "John" for example
```
In addition to the comparison operators we can include also the following operator in the where clause:
- `between` - filter records within a numeric or time and date range
- `like` - specify a pattern within the search criteria. '%' is used as wildcard for the condition to be matched
- `in` - specify multiple possible values for a column

| **Operator** | **Description** |
| --- | --- |
| **ALL** |  Used to compare a single value to all the values in another value set. |
| **AND** | Allows for the existence of multiple conditions in an SQL statement's WHERE clause. |
| **ANY** | Used to compare a value to any applicable value in the list as per the condition. |
| **BETWEEN** | Used to search for values that are within a set of values, given the minimum value and the maximum value. |
| **EXISTS** | Used to search for the presence of a row in a specified table that meets a certain criterion. |
| **IN** | Used to compare a value to a list of literal values that have been specified. |
| **LIKE** | Used to compare a value to similar values using wildcard operators. |
| **NOT** | Reverses the meaning of the logical operator with which it is used. For example: NOT EXISTS, NOT BETWEEN, NOT IN, etc. **This is a negate operator.** |
| **OR** | Used to combine multiple conditions in an SQL statement's WHERE clause. |
| **IS NULL** | Used to compare a value with a NULL value. |
| **UNIQUE** | Searches every row of a specified table for uniqueness (no duplicates). |

### SELECT DISTINCT clause

Select only distinct value without duplicates.
```mysql
SELECT DISTINCT country
FROM tab;
```

#### Schema Design
```mysql
CREATE TABLE table_name(   
    column_name1 INT, 
    column_name2 VARCHAR(255), 
    column_name3 FLOAT, 
    column_name4 INT, 
    column_name5 INT, 
    PRIMARY KEY (column_name1), 
    FOREIGN KEY (column_name2) REFERENCES table_name2(column_table2) 
);
```
Foreign key statement list `column_name2` as the field in the `table_name` table that reference `column_table2` in the table `table_name2`.