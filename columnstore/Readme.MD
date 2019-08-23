# MariaDB ColumnStore Docker Container Setup

## Introduction
This docker image will startup a single server instance of MariaDB ColumnStore running on CENTOS 7. It is designed and suitable for demo and evaluation.


## Requirements
The following are required:
- A computer with a 64bit OS, CPU virtualization extensions enabled in the BIOS and minimum 8GB RAM
- Docker (configured with at least 4GB memory for the Docker VM if on Windows or Mac)
- This git repository


## Run Single Instance Container

To build the latest MariaDB ColumnStore version run the following command:
```
make build-image
```

To run MariaDB ColumnStore run the following command:
```
docker run -d --name mcs mariadb/columnstore:latest
```

To specify a root password:
```
docker run -d --name mcs -eMARIADB_ROOT_PASSWORD=mypassword mariadb/columnstore:latest
```

The following output will be seen indicating the server is up:

```sh
Starting MariaDB Columnstore Database Platform

MariaDB ColumnStore Database Platform Starting, please wait ....... DONE

System Catalog Successfully Created

MariaDB ColumnStore Install Successfully Completed, System is Active

Enter the following command to define MariaDB ColumnStore Alias Commands

. /usr/local/mariadb/columnstore/bin/columnstoreAlias

Enter 'mcsmysql' to access the MariaDB ColumnStore SQL console
Enter 'mcsadmin' to access the MariaDB ColumnStore Admin console

NOTE: The MariaDB ColumnStore Alias Commands are in /etc/profile.d/columnstoreAlias.sh

Container initialization complete at Tue Jul 24 05:16:48 UTC 2018
```

## Customization
The following environment variables can be utilized to configure behavior:
* MARIADB_ROOT_PASSWORD : specify the password for the root user
* MARIADB_ALLOW_EMPTY_PASSWORD : allow empty password for the root user
* MARIADB_RANDOM_ROOT_PASSWORD : generate a random password for the root user (output to logs). Note: This option takes precedence over MARIADB_ROOT_PASSWORD.
* MARIADB_INITDB_SKIP_TZINFO : skip timezone setup
* MARIADB_ROOT_HOST : host for root user, defaults to '%'
* MARIADB_DATABASE : create a database with this name
* MARIADB_USER : create a user with this name, with all privileges on MARIADB_DATABASE if specified
* MARIADB_PASSWORD : password for above user
* MARIADB_CS_POSTCFG_INPUT : override input values for postConfigure. The default value in the Dockerfile will start up a single server deployment. If the environment variable is empty then postConfigure will not be run and the container will just run the ColumnStore service process ProcMon.
* MARIADB_CS_NUM_BLOCKS_PCT - If set uses this amount of physical memory to utilize for disk block caching. Explicit amounts need to be suffixed with M or G. Will override the default setting of 1024M from Dockerfile.
- MARIADB_CS_TOTAL_UM_MEMORY - If set uses this amount of physical memory to utilize for joins, intermediate results and set operations on the UM. Explicit amounts need to be suffixed with M or G. Will override the default setting of 256M from Dockerfile.
* MARIADB_DROP_LOCAL_USERS : Drop anonymous local users, useful for  removing this on non um1 um containers.

Note that the ColumnStore docker image differs from the MariaDB server docker image in that  it does not mandate specification of one of the first 3 password entries. This is for backward compatibility and also due to the fact that specifying these for a multi UM deployment is unreliable / not recommended due to timing issues with the current setup and replication.

### Custom scripts
Custom scripts can be mapped as a host volume to /docker-entrypoint-initdb.d for execution post initialization in named / directory order. The following extensions are supported:
* .sh : shell script
* .sql : sql script
* .sql.gz : gzip compressed sql script

## Multi Node Cluster compose file
- If not already built, you have to build the latest MariaDB ColumnStore 1.2 version using following command:

```
docker build -t mariadb/columnstore:1.2 .
```

- The docker-compose.yml file will bring up a 1um 2pm cluster using local storage
per container to allow easy evaluation of a multi node cluster. To run this:

```sh
$ docker-compose up -d
```

- To verify the cluster is up and active follow logs on the pm1 node and look for
similar output to the single node deployment:

```sh
$ docker-compose logs -f
```

Assuming you have the mariadb client installed on the host, you can connect to um1:
```sh
mysql -udbuser -pdbpassword -h127.0.0.1 -P3306 mydb
```

The root password is randomly generated and output to docker logs.

- To stop the containers and remove volumes:

```sh
$ docker-compose down -v
```
