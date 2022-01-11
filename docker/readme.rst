============
MySQL Server
============

About
=====
**MySQL** [1]_ is a popular open source, relational database management system.

Version
-------
MySQL Server **Community Edition** version **8.0.27** deployed based on Docker Hub image: [2]_, 
which is created, maintained and supported by the MySQL team at Oracle. 

License
-------
**GPL** [3]_

Pre-requisites
==============
* *docker* installed
* access to DIGITbrain private docker repo (username, password) to pull the image:
  
  - ``docker login dbs-container-repo.emgora.eu``
  - ``docker pull dbs-container-repo.emgora.eu/mysql-server:8.0.27``

Usage
=====
.. code-block:: bash

  docker run -d --rm --name mysql \
    -e MYSQL_DATABASE=mydatabase \
    -e MYSQL_USER=mydatabaseuser \
    -e MYSQL_PASSWORD=mydatabasepassword \
    -p 3306:3306/tcp \
    mysql-server:8.0.27

where MYSQL_DATABASE parameter is the name of an initial database to be created, 
MYSQL_USER and MYSQL_PASSWORD parameters create a new database user with the given username and password,
and standard MySQL port 3306 is opened on the host.

.. warning::
  Always update the ``MYSQL_USER`` and ``MYSQL_PASSWORD`` parameters with the values of your choice
  prior to running this container.

Security
========
The image uses **SSL/TLS traffic encryption** and **username-password authentication**.

.. warning::
  Although SSL/TLS is enabled by default in MySQL, when a client connects 
  without certificate or any --ssl option, the connection falls back to unencrypted.
  You can check whether SSL is in use:

  .. code-block:: bash

    docker exec -it mysql mysql -uroot -p --ssl-ca=/etc/certs/ca.pem
    mysql> status
      ...
      SSL:			Cipher in use is TLS_AES_256_GCM_SHA384

Consider using --ssl-mode=VERIFY_IDENTITY as a best practice. The server certificate used in the image is 
signed by DIGITbrain certificate authority, but obviously host name verification will fail. 
You should use the server certificate of your own (see Configuration, ssl-* volumes below).
TLS client authentication is also configurable (see [4]_).

Configuration
=============
A more a customized run example:

.. code-block:: bash
  docker run -d --rm --name mysql \
    -v $PWD/data:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=mysqlrootpassword
    -e MYSQL_DATABASE=mydatabase \
    -e MYSQL_USER=mydatabaseuser \
    -e MYSQL_PASSWORD=mydatabasepassword \
    -p 3306:3306/tcp \
    mysql-server:8.0.27

where we set 'root' password for MySQL database server and persist MySQL data on host directory: ``./data``.

See parameters and volumes below for explanation.

Parameters
----------
.. list-table:: 
   :header-rows: 1

   * - Name
     - Example
     - Comment
   * - *database*
     - ``-e MYSQL_DATABASE=mydatabase``
     - An initial database to create
   * - *root password*
     - ``-e MYSQL_ROOT_PASSWORD=mysqlrootpassword``
     - Root password for MySQL
   * - *username*
     - ``-e MYSQL_USER=mydatabaseuser``
     - Username for a newly created user
   * - *password*
     - ``-e MYSQL_PASSWORD=mydatabasepassword``
     - Password for a newly created user

Ports
-----
.. list-table:: 
  :header-rows: 1

  * - Container port
    - Host port bind example
    - Comment
  * - *3306*
    - ``-p 13306:3306``
    - Default MySQL container port 3306 is opened as port 13306 on the host  

Volumes
-------
.. list-table:: 
  :header-rows: 1

  * - Name
    - Volume mount example
    - Comment
  * - *MySQL data*    
    - ``-v $PWD/data:/var/lib/mysql``
    - MySQL data will be persisted in host directory: ``./data``.
  * - *MySQL CA certificate*    
    - ``-v $PWD/certificates/ca.pem:/etc/certs/ca.pem``  
    - Certificate Authority caertificate
  * - *MySQL server key*    
    - ``-v $PWD/certificates/server-key.pem:/etc/certs/server-key.pem``  
    - Server key file in PEM format
  * - *MySQL server certificate*    
    - ``-v $PWD/certificates/server-cert.pem:/etc/certs/server-cert.pem``  
    - Server certificate in PEM format

References
==========
.. [1] https://www.mysql.com/ 

.. [2] https://hub.docker.com/r/mysql/mysql-server/  

.. [3] https://www.mysql.com/products/community/ 

.. [4] https://www.howtoforge.com/tutorial/how-to-enable-ssl-and-remote-connections-for-mysql-on-centos-7/ 

