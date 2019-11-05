
.. _integrate_sql_database:



SQL database
--------------------------


Integrating a SQL database is quite simple. You need to declare a data store 
of the type of the database you want to integrate

Each database have specificities that are described at their pages :

.. toctree::
   :maxdepth: 1

   databases/postgresql
   databases/mysql
   databases/h2

   

The rest of this page details generalities applicable to most of the databases.

General parameters
===================

All those parameters can be set as XML attributes of the datastore element 
or as configuration with dw.datastore.<*datastore_name*>.<*parameter*>.

============================ ==========
Parameters                   Details
============================ ==========
name                         how you want this data store be refered as
type                         depend on the type of database 
                             (mysql for MySQL and postgresql for PostgreSQL)
host                         IP or DNS of the database location
database                     Which database to connect to
user                         user of the database to use
password                     password of the database
sshUser                      (optionnal) If specified, DataFactory will establish 
                             a `SSH tunnel <htthttps://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604>`_
                             with this user name
sshPassword                  (optionnal) If there is a sshUser, Datafactory will use this as the ssh
                             password. If there is a sshUser
                             but no sshPassword DataFactory will 
                             try to use its ssh private key
sshPrivateKeyLocation        Location of the private key file
sshPrivateKeyFilePassphrase  Optional passphrase for the private key
extendedConnectionParameters Connection parameters to add to defaults ones 
                             (see each database description)
connectionParameters         Connection parameters to use, discards default ones
                             (see each database description)
============================ ==========


In case you want to use SSH tunneling but not use the password 
authentification. you need to add your public key to the 
~/.ssh/authorized_keys file on *host* for the *sshUser*.


Autodiscovery of metadata
==========================

As the database already contains all the needed informations about 
tables and columns, it is not mandatory to detail them. There is 3
ways to describe a SQL datastore :

All informations 
################

Like any datastore, you can provide all tables and columns informations.
This is convenient to either : 

 - ensure the database layout (safety)
 - is a bit faster (no metadata search)
 - allow to use only some columns of tables (like not using columns with 
   sensitive data or avoiding useless columns)

But obviously it is more time consuming and error prone to detail everything.

An exemple can be found below :

.. code-block:: xml

  <datastore name="db1" type="postgresql" host="db.theowner.com" database="database" user="john" password="Doe">
    <table name="cutsomers" schema="public">
      <column name="id" type="int"/>
      <column name="name" type="text"/>
    </table>
  </datastore>

Just table names
################

Another way is to specify only table names. Data Brewery will automatically 
ask for the metadata as needed. In this case, it will take all commun with 
the most adequate column type.

The previous example will be simplified as :

.. code-block:: xml

  <datastore name="db2" type="postgresql" host="db.theowner.com" database="database" user="john" password="Doe">
    <table name="cutsomers" schema="public"/>
  </datastore>


Auto discovery of a schema
############################

The laziest way to add a SQL datastore is to use the autoDiscovery element to 
automatically add every tables of a given schema.

The example below will add all tables in the public and my_schema schemas to 
the datastore. If there is a conflict (two tables with the same name),
the first one will be used.

.. code-block:: xml

  <datastore name="db3" type="postgresql" host="db.theowner.com" database="database" user="john" password="Doe">
    <autoDiscovery schema="public"/>
    <autoDiscovery schema="my_schema"/>
  </datastore>




About schemas
=============

Most databases use schemas to organize data. Some databases (Mysql for 
instance) mix the database and schema concepts. Tables can have the same
name but stay in different schemas. That's why you should specify the schema
when describing a table in a SQL datastore.

While not recommended, schema can be left empty (by not defining it or by 
setting it to ""). In such case, Data Brewery will use the search path of 
the database (which might differ for each database).


Using SSH tunnels
==================

In order to secure a database, it is sometimes not accessible from the 
network. In such case, the client must first establish a SSH tunnel
to the server than connect locally to the database. 

Data Brewery support such scenario with ssh parameters.

A simple example is to provide a sshUser and a sshPassword as below :

.. code-block:: xml

  <datastore name="db1" type="postgresql" host="db.theowner.com" database="database" 
    user="john" password="Doe"
    sshUser="john" sshPassword="Doe"/>

A more secured way to connect is to use a public/private keys pair (see 
`here <https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-on-ubuntu-1604>`_
for how to set a public/private keys pair). 
The *sshPrivateKeyFilePassphrase* is optionnal depending if you have
set a passphrase for your key.

.. code-block:: xml

  <datastore name="db1" type="postgresql" host="db.theowner.com" database="database" 
    user="john" password="Doe"
    sshUser="john" 
    sshPrivateKeyLocation="keys/private.key"
    sshPrivateKeyFilePassphrase="Doe"
    />


Using connection parameters
============================

We use JDBC to connect to databases and you can add some configuration to it.
Some datastore type already add some paramaters (see each datastore 
documentation). You can either add more paramaters with the parameter
*extendedConnectionParameters* or reset the whole parameters list
with *connectionParameters*.

Each parameter is separated by &. As it is an unautorized character,
you should use the escaped version &amp; instead. For instance :

.. code-block:: xml

  <datastore name="prestashop" type="mysql" 
    connectionParameters="serverTimezone=UTC&amp;useSSL=false"/>

