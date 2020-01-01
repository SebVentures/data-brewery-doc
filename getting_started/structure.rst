.. _structure:

Project structure
=================

In `Data Brewery <https://databrewery.co/>`_ your data environment is described 
by a set of XML files, some `HOCOM (Human-Optimized Config Object Notation) 
files <https://github.com/lightbend/config/blob/master/HOCON.md#hocon-human-
optimized-config-object-notation>`_ (think JSON on steroid) and SQL files.

One file project
----------------

In its simplest form, you can have the following file dw.xml that define 
a :ref:`datastore <specs_datastore>` and a :ref:`module <specs_module>`.

.. code-block:: xml

    <datawarehouse>
      <datastore name="dw" type="h2" host="file"/>

      <module name="db" datastore="dw">
        <table name="dummy">
          <source type="query">select current_date</source>
        </table>
      </module>
    </datawarehouse>


To help you get started, you can initiate a working seed (the example which will 
be detailled in the next pages) by calling the following command in a new directory :

.. code-block:: bash

   ipa init simple


There is also a seed for this tutorial, so you can just call :

.. code-block:: bash

   ipa init earthquake

Which will create the dw.xml file used for the rest of the tutorial and the final Excel report template.

You can continue to read this page to know more about complex project 
structuration or go directly to the first tutorial step :ref:`accessing data with datastores <tuto_external_data>`.

Complex projects
----------------

While it work fine, when the projects gets bigger it make sense to break 
the file in many part.

.. _separated_configuration:

Separated configuration
########################

The first step is to introduce configurations files.
By default, *ipa* read the *dw.conf* file in the current path.
It allows to override existing or missing parameters.

For instance, having the following dw.xml and dw.conf files, 
the password for the *mydb* datastore will be "the_real_password"
instead of the one declared in the XML.

.. code-block:: xml

  <!-- dw.xml -->
  <datawarehouse>
    <datastore name="mydb" type="postgresql" host="mydb.myproject.com" user="admin" password="mypassword"/> 
  </datawarehouse>


.. code-block:: json

  # dw.conf
  dw.datastore.mydb {
    password = "the_real_password"
  }



.. _environment_configuration:

Environment configuration
##########################

Now, you may want to manage multiple environments. For instance, you might have a development
environment where your data warehouse is stored on your local computer and using a
production environment with the dedicated data warehouse. You can easily manage that
scenario by using different configurations files. The *dw.conf* file will
reference low sensitivity parameters while the *prod.conf* will reference the 
default *dw.conf* parameters and overload some of them with the productions 
parameters.

.. code-block:: json

  # dw.conf (dev)
  dw.datastore.dw {
    host = "localhost"
    database = "datawarehouse"
    password = "the_real_password"
  }

.. code-block:: json

  # conf/prod.conf (prod)

  include "../dw.conf"
  
  dw.datastore.dw {
    host = "dw.myproject.com"
    database = "datawarehouse"
    password = "the_real_password"
    sshUser = "myid"
    sshPrivateKeyLocation = "keys/etl"
  }

In order run a process with *ipa*, you can set the main configuration file with 
the following command line to execute the *nightly* process in production mode.

.. code-block:: bash

   ipa run-process -c conf/prod.conf nightly


Externalize SQL queries
##########################

SQL queries are core to `Data Brewery <https://databrewery.co/>`_ projects.
While it is possible to keep them in the project file,
a common approach is to externalize the big ones (> 5 lines) to their
own SQL file. Being XML, the project file doesn't allow use of some
common characters like '<'. One way is to protect the SQL query with 
CDATA block, but putting them in a distinct SQL file is easier and
enable syntax highlightning.

To externalize a SQL query, you just need to link the file with the 
*contentPath* attribute. The path is relative to the file
where it is located.

.. code-block:: xml

  <module name="business" datastore="dw">
    <table name="d_date">
      <source type="query" contentPath="business/d_date.sql"/>
    </table>  
  </module>

.. code-block:: sql

  -- business/d_date.sql
  select to_char(datum,'yyyymmdd')::int as date_key,
    datum as "date",
    extract(epoch from datum)::bigint as epoch,
    to_char(datum,'Day') as day_name,
    extract(isodow from datum)::int as day_of_week,
    extract(day from datum)::int as day_of_month
    -- ...
  from (select '2000-01-01'::date + day as datum
    from generate_series (0,29219) as day) t
  order by 1 desc;



Project file breakdown
###########################

Finally, it is also possible to split the project file (the
XML) in many part.

For that, you just need to insert an *include* element to link
to a child file which will replace the include element.

For instance, the following project file :

.. code-block:: xml

  <datawarehouse>
    <datastore name="dw" type="h2">
      <!-- The content -->
    </datastore>
  </datawarehouse>


is equivalent to the combination of the next two ones.

.. code-block:: xml

  <datawarehouse>
    <include path="store/dw.xml"/>
  </datawarehouse>
  
.. code-block:: xml

  <!-- store/dw.xml -->
  <datastore name="dw" type="h2">
    <!-- The content -->
  </datastore>

It is a good practice to keep project file below 100 lines of code.

Inside the path attribute, you can use basic pattern matching with the 
'*' character. For instance, "dir/*/*.xml" will include every file
that is inside a directory in the "dir" directory and ends with  
".xml".

Full project structuration
###########################

By providing all the previously mentionned project structuration 
mecanism, `Data Brewery <https://databrewery.co/>`_  encourage a 
lot a flexibility.

To wrap up, let's present the idiomatic layout a complex project
should have.

* conf : configuration environment files (test.conf, prod.conf) 
* key : location to store SSH keys and others authentification mecanisms
* module : location for :ref:`modules <specs_module>` 

  - mod1.xml : Module mod1 definition
  - mod1     : directory for mod1 SQL files

    + d_date.sql : SQL file for the date dimension
    + f_kpi.sql  : SQL file for the 

* store : location for  :ref:`datastores <specs_datastore>` 
* dw.conf : Default configuration file (dev environment)
* dw.xml  : The main project file.

You can initialize such a structured project with the following command :

.. code-block:: bash

  ipa init complex


Let's now see the first step in the process : 
:ref:`accessing data with datastores <tuto_external_data>`.

