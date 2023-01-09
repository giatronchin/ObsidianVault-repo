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