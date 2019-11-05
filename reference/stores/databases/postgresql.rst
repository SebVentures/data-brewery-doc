
.. _store_postgresql:

PostgreSQL
--------------------------

PostgreSQL is a quite common database and it is recommanded for a small data warehouse (less than 100GB of data). You can `read here a more detailed discussion <https://dataintoresults.com/post/postgresql-for-data-science-pro-and-cons/>`_ for using PostgreSQL as a data warehouse.

Below is a simple example for a PostgreSQL connection.

.. code-block:: xml

    <!-- Define where the data warehouse is located. -->
    <datastore name="dw" type="postgresql" host="dw.theowner.com" database="datawarehouse" user="john" password="Doe">
    </datastore>               


Connection parameters
=====================

By default, Data Brewery set those connection parameters :

============================ =========================================== =============== 
Connection parameter         Value                                       Reason 
============================ =========================================== =============== 
sslmode                      prefer                                      Prefer, but don't enforce SSL connection 
sslfactory                   org.postgresql.ssl.NonValidatingFactory     Don't fail is the server certificate isn't valid 
============================ =========================================== =============== 
