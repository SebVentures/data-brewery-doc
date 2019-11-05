
.. _store_databases:



SQL databases
--------------------------


Integrating a SQL database is quite simple. You need to declare a data store of the type of the database you want to integrate

Each database have specificities that are described at their pages :

.. toctree::
   :maxdepth: 1

   databases/postgresql
   databases/mysql
   databases/h2

The rest of this page details generalities applicable to most of the databases.


=============== ==========
Parameters      Details
=============== ==========
name            how you want this data store be refered as
type            depend on the type of database (mysql for MySQL and postgresql for PostgreSQL)
host            IP or DNS of the database location
database        Which database to connect to
user            user of the database to use
password        password of the database
sshUser         (optionnal) If specified, DataFactory will establish a `SSH tunnel <http://study.com/academy/lesson/ssh-proxy-tunnels.html>`_
                with this user name
sshPassword     (optionnal) If there is a sshUser, Datafactory will use this as the ssh
                password. If there is a sshUser but no sshPassword DataFactory will try to use its ssh private key
=============== ==========


In case you want to use SSH tunneling but not use the password authentification. You need to add the following text to the ~/.ssh/authorized_keys file on *host* for the *sshUser*.


.. code-block:: console

    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD0yVYf/IKwPRID+VXfiXvtj8iQ3HP204MLl4wFCBs/tIiVBkTUe7AlRaAuQj0vdN1itEdRs77Puiw6CPvcmecWBGjpV9cU0BoihwJZyyrvDTLOsm2AjTWSUjTBb3D/Ch/8DFSopPCYzoSvoyvhHH4TPmncl443GlDnymgw7+9jIbMUxKxjbf44pLEC2HkBr/y7dPFAwoNrmM6Bq9uasoknvKv1H6a34OwVK6/q5TkAB0XbYMo18VqKguqyQ3V3QV4CVan4l2ePkWOIYieMZElgdeV+GJ39iuBhciPycwgFFQJBLToPYqu3NkQICLVFzXkTEKugIw3pGPFTfU1V4eD7 datafactory@dataintoresults.com

And here is a simple example using ssh tunneling. Notice the use of *autodiscovery* tag which tell DataFactory to use SQL metadata to find out which tables are present in the database.


.. code-block:: xml

    <dataWarehouse>
        <!-- Define where the data warehouse is located. -->
        <datastore name="dw" type="postgresql" host="dw.theowner.com" database="datawarehouse" 
            user="john" password="Doe" sshUser="john">
        </datastore>

        <!-- Define where the ERP (prestashop) is located. -->
        <datastore name="prestashop" type="mysql" host="theowner.com" database="prestashop" 
            user="john" password="Doe" sshUser="john">
            <!-- With autodiscover, DataFactory will analyse the prestashop schema of the database to get tables.  -->
            <autodiscovery schema="prestashop"/>
        </datastore>

        <!-- Integration layer module : prestashop  -->
        <!-- In the database it will be store under the m901_prestashop schema  -->
        <module id="901" name="prestashop" datastore="dw">
            <!-- We replicate all the prestashop datastore-->
            <replicate datastore="prestashop"/>
        </module>
    </dataWarehouse>                     
