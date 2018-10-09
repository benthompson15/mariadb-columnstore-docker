# MariaDB ColumnStore Docker Repository
This repository contains varying docker configurations for MariaDB ColumnStore:
- *columnstore* : Base ColumnStore server docker image.
- *columnstore_jupyter* : Demonstrates the MariaDB ColumnStore Spark Connector running with a Jupyter notebook.
- *columnstore_zeppelin* : AX Sandbox demo illustrating analytics on a book store data set.

# Initializing a fresh instance
When a container is started for the first time, a new database with the name, specified in CS_DATABASE env variable, will be created. Furthermore, it will execute files with extensions .sh, .sql and .sql.gz that are found in /docker-entrypoint-initdb.d. Files will be executed in alphabetical order. You can easily populate your ColumnStore services by mounting a SQL dump into that directory and provide custom images with contributed data. SQL files will be imported to the database specified by the CS_DATABASE variable. Container uses database 'test' as a fallback.

---

-	[Travis CI:  
	![build status badge](https://img.shields.io/travis/mariadb-corporation/mariadb-columnstore-docker/master.svg)](https://travis-ci.org/mariadb-corporation/mariadb-columnstore-docker/branches)
