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
    -e MYSQL_USER=myuser \
    -e MYSQL_PASSWORD=mypassword \
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
  without a certificate, the connection falls back to non-encrypted one.
  You can check whether SSL is in use:

  .. code-block:: bash

    docker exec -it mysql mysql -uroot -p --ssl-ca=/etc/certs/ca.pem
    mysql> status
      ...
      SSL:			Cipher in use is TLS_AES_256_GCM_SHA384

Consider using ``--ssl-mode=VERIFY_IDENTITY`` as a best practice. However, the default server certificate in this docker image
had been signed by the DIGITbrain Certificate Authority with the host IP inside that will not match your host IP, and so hostname verification will fail.
Use a server certificate of your own (with a proper IP or SAN) to enable this mode (see Configuration of certificates below) or 
use  ``--ssl-mode=VERIFY_CA`` or ``--ssl-mode=REQUIRED`` to bypass hostname verification.
TLS client authentication is also configurable (see [4]_).

Configuration
=============

Environment variables
---------------------
.. list-table:: 
   :header-rows: 1

   * - Name
     - Example
     - Comment
   * - *MYSQL_DATABASE*
     - ``-e MYSQL_DATABASE=mydatabase``
     - An initial database to create
   * - *MYSQL_ROOT_PASSWORD*
     - ``-e MYSQL_ROOT_PASSWORD=mysqlrootpassword``
     - Root password for MySQL
   * - *MYSQL_USER*
     - ``-e MYSQL_USER=myusername``
     - Username for a newly created user
   * - *MYSQL_PASSWORD*
     - ``-e MYSQL_PASSWORD=mypassword``
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
  * - *CA certificate*    
    - ``-v $PWD/certificates/ca.pem:/etc/certs/ca.pem``  
    - Certificate Authority certificate (in PEM format)
  * - *Server certificate*    
    - ``-v $PWD/certificates/server-cert.pem:/etc/certs/server-cert.pem``  
    - Server certificate (in PEM format)
  * - *Server key*    
    - ``-v $PWD/certificates/server-key.pem:/etc/certs/server-key.pem``  
    - Server key file (in PEM format)

References
==========
.. [1] https://www.mysql.com/ 

.. [2] https://hub.docker.com/r/mysql/mysql-server/  

.. [3] https://www.mysql.com/products/community/ 

.. [4] https://www.howtoforge.com/tutorial/how-to-enable-ssl-and-remote-connections-for-mysql-on-centos-7/ 

