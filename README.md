# General

## Deployment type

Docker

## Image

Based on official image on Docker Hub: https://hub.docker.com/r/mysql/mysql-server

## Licence

MySQL Community Edition, GPL License

## Version

8.0.23

## Description

MySQL is one of the most popular relational database management system (RDBMS). Data are organised into tables, tables have columns, entries are rows. 


# Deployment

General example:

```
docker run -d --rm \
        --name mysql
        -p 33306:3306 \
        mysql/mysql-server:8.0.23
```
## Parameters

|Name|Value|Description|
|-|-|-|
|Ports| `-p 33306:3306` | MySQL port |
|Volume| `-v $HOME/mysqldata:/var/lib/mysql` | Persist MySQL data |
|Environment| `-e MYSQL_DATABASE=mydatabase` | Initial database |
|Environment| `-e MYSQL_ROOT_PASSWORD=mysqlrootpassword` | Root password |
|Environment| `-e MYSQL_USER=mydatabaseuser` | User username |
|Environment| `-e MYSQL_PASSWORD=mydatabasepassword` | User password |

## Test:

Run *mysql* client:

> docker run -it mysql mysql

Note: 
- use -hsome.mysql.host to connect to a remote MySQL host

# Authentication

See environment variables: MYSQL_USER, MYSQL_PASSWORD.
Run:

```
docker run -d --rm \
        --name mysql
        -p 33306:3306 \
        MYSQL_DATABASE=test \
        -e MYSQL_USER=testuser \
        -e MYSQL_PASSWORD=testpass \
        mysql/mysql-server:8.0.23
```

## Test

Run *mysql* client with username (testuser) and password (promted: testpass) :

> docker run -it mysql mysql -utestuser -p


# TLS

Notes [1]: 
- TLS is enabled by default, but when a client connects to MySQL server without providing proper TLS certificates, connection falls back to unencrypted one
- MySQL automatically generates cerificates at startup in its data dir: /var/lib/mysql: ca.pem, private_key.pem, server-key.pem
- you can use your own certs, if you specify them in /etc/my.cnf file: ssl-ca=/.../ca.pem, ssl-cert=/.../server-cert.pem, ssl-key=/.../server-key.pem

## Test

Fall back to non-TLS:

```
docker exec -it mysql-server mysql -p
mysql> status
mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
...
SSL:			Not in use
```

TLS:
```
docker exec -it mysql-server mysql -p --ssl-ca=/var/lib/mysql/ca.pem
mysql> status
mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
...
SSL:			Cipher in use is ECDHE-RSA-AES128-GCM-SHA256
```


# TLS mutual

Users can be forced to use TLS [1]:

```
create user 'user'@'%' identified by 'pass' REQUIRE X509;
grant all privileges on *.* to 'user'@'%' identified by 'pass' REQUIRE X509;
flush privileges;
```


## Test

TLS with client authentication (user REQUIRE X509): 

```
docker exec -it mysql-server mysql -p --ssl-ca=/var/lib/mysql/ca.pem --ssl-cert=/var/lib/mysql/client-cert.pem --ssl-key=/var/lib/mysql/client-key.pem

mysql> status
mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
...
SSL:			Cipher in use is ECDHE-RSA-AES128-GCM-SHA256
```

# References

[1] https://www.howtoforge.com/tutorial/how-to-enable-ssl-and-remote-connections-for-mysql-on-centos-7/


