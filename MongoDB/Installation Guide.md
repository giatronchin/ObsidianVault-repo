The following code demonstrates how to install and use `mongosh` in a Linux environment.

1. Update `apt` and install `gnupg`. Then add the MongoDB public GPG key to the system.

```
apt update 
apt install <code>gnupg</code>
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add - 
```

  
4. Create a list file for MongoDB.

```
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu 
focal/mongodb-org/6.0 multiverse" | sudo tee 
/etc/apt/sources.list.d/mongodb-org-6.0.list
```

  
7. Update `apt` and install `mongosh`.

```
apt update
apt install -y mongodb-mongosh
```

  
10. Check that `mongosh` is installed.

```
mongosh --version
```

  
13. Exit `mongosh`.

```
exit
```

## Connect to Your Atlas Cluster by Using mongosh

The following code demonstrates how to connect to your MongoDB Atlas cluster by using `mongosh`.

1. Run the `mongosh` command followed by your connection string.

```
mongosh "mongodb+srv://<username>:<password>@<cluster_name>.example.mongodb.net"
```

Alternatively, you can log in by providing the username and password as a command line argument with `-u` and `-p`.

```
mongosh -u exampleuser -p examplepass "mongodb+srv://myatlasclusteredu.example.mongodb.net"
```

  
7. After connecting to the Atlas cluster, run `db.hello()`, which provides some information about the role of the `mongod` instance you are connected to.

```
db.hello()
```

  
10. Finally, run the `exit` command inside `mongosh` to go back to the terminal.

```
exit
```