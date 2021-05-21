# bb-mysql
Building block for MySQL

# IMAGE SOURCE

Official image on __Docker Hob__: 

Licence: MySQL Community Edition, GPL License

# DEPLOYMENT

> docker run -p 33306:3306 --name mysql-server -e MYSQL_ROOT_PASSWORD=r00tpass -d mysql/mysql-server:8.0.23


Further options:
VOLUMES:
        -v $HOME:/var/lib/mysql

OPTIONS:

        -e MYSQL_DATABASE=mydatabase
        -e MYSQL_USER=mydatabaseuser
        -e MYSQL_PASSWORD=mydatabasepassword

EXAMPLE:
docker run --name mysql-server -p 33306:3306 -e MYSQL_ROOT_PASSWORD=r00tpass -e MYSQL_DATABASE=test -e MYSQL_USER=testuser -e MYSQL_PASSWORD=testpass -d mysql/mysql-server:8.0.23

TEST:

> docker run -it --rm mysql mysql -hsome.mysql.host -usome-mysql-user -p


# TLS

__Enabled by default__, but when a client connects to MySQL server without providing proper TLS certificates, connection falls back to unencrypted one.

MySQL automatically generates cerificates at startup in its data dir: /var/lib/mysql: __ca.pem, private_key.pem, server-key.pem__.


See:
https://www.howtoforge.com/tutorial/how-to-enable-ssl-and-remote-connections-for-mysql-on-centos-7/

__Note:__ you can use your own certs, if you specify them in /etc/my.cnf file: ssl-ca=/.../ca.pem, ssl-cert=/.../server-cert.pem, ssl-key=/.../server-key.pem.

## TEST

Fall back to non-TLS:

> docker exec -it mysql-server mysql -p
>
> mysql> status
>
> mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
>
> ...
>
> __SSL:			Not in use__

TLS:

> docker exec -it mysql-server mysql -p __--ssl-ca=/var/lib/mysql/ca.pem__
>
> mysql> status
>
> mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
>
> ...
>
> __SSL:			Cipher in use is ECDHE-RSA-AES128-GCM-SHA256__



# TLS mutual authentication

Users can force to use TLS:

> create user 'user'@'%' identified by 'pass' REQUIRE X509;
>
> grant all privileges on *.* to 'user'@'%' identified by 'pass' REQUIRE X509;
>
> flush privileges;

See:
https://www.howtoforge.com/tutorial/how-to-enable-ssl-and-remote-connections-for-mysql-on-centos-7/

## TEST
TLS with client authentication (user REQUIRE X509): 

> docker exec -it mysql-server mysql -p __--ssl-ca=/var/lib/mysql/ca.pem --ssl-cert=/var/lib/mysql/client-cert.pem --ssl-key=/var/lib/mysql/client-key.pem__
>
> mysql> status
>
> mysql  Ver 8.0.23 for Linux on x86_64 (MySQL Community Server - GPL)
>
> ...
>
> __SSL:			Cipher in use is ECDHE-RSA-AES128-GCM-SHA256__
