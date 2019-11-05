
.. _store_mysql:

MySQL / MariaDB
--------------------------

Integrating a SQL database is quite simple. You need to declare a data store of the type of the database you want to integrate

We don't recommand to use MySQL or MariaDB for a significant data warehouse. However, MariaDB is prefered. For a longer discussion `see here <https://dataintoresults.com/post/mysql-mariadb-for-data-science-pro-and-cons/>`_.

The main thing to consider is that for MySQL there is no difference between a database and a schema.

For instance, you can find below a MySQL datastore that connect to a MySQL database and automatically discover the tables under the prestashop schema.

.. code-block:: xml

    <datastore name="prestashop" type="mysql" host="theowner.com" database="prestashop" 
        user="john" password="Doe">
        <!-- With autodiscover, DataBrewery will analyse the prestashop schema of the database to get tables.  -->
        <autodiscovery schema="prestashop"/>
    </datastore>


Default connection parameters
=============================


============================ =============== ===============
Connection parameter         Value           Reason
============================ =============== ===============
zeroDateTimeBehavior         convertToNull   Avoid having unparsable dates like '0000-00-00'
autoReconnect                true            Reconnect if the connection is lost
characterEncoding            UTF-8           Internally Data Brewery is in UTF-8
characterSetResults          UTF-8           Internally Data Brewery is in UTF-8
============================ =============== ===============


Interesting connection parameters
==================================


Issues with server timezone
###########################

MySQL have some timezone that aren't recognized by JDBC (our 
internal connection framework). In such case, you can 
enforce a more traditionnal timezone (like UTC)

.. code-block:: xml

    <datastore name="prestashop" type="mysql" 
        extendedConnectionParameters="serverTimezone=UTC"/>


