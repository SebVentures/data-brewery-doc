
.. _store_oracle:

Oracle
--------------------------

`Oracle <https://www.oracle.com/database/>`_ is one of the oldest SQL 
database around.

There is some significant changes from other SQL databases:

- The date type in Oracle contains hours, minutes and seconds. Therefore,
  we treat date as datetime.
- Oracle is case sensitive for identifiers but uppecase unprotected identifiers 
  (see :ref:`case handling <store_oracle_case_handling>`).
  We recommand to use uppercase identifiers only.
- In Oracle, a schema is in fact a user. Therefore for each schema 
  (each :ref:`module <specs_module>` for instance) a corresponding user 
  will be created.
- There is an extra attribute *defaultTablespace* which will set the default tablespace
  when a schema/user is created. By default, it is set to *USERS*.

You can see a full listing `on connection possibilities on H2 website <http://www.h2database.com/html/features.html#database_url>`_
. We just add a semicolumn ":" between *host* and *database* when *host* 
doesn't contain one already.

Here is the code for a memory based datastore (by default) :

.. code-block:: xml

    <!-- Define where the data warehouse is located. -->
    <datastore name="dw" type="oracle" host="dw.theowner.com" database="datawarehouse" user="john" password="Doe">
    </datastore>               



.. _store_oracle_case_handling:

Case handling
=============

By default, Oracle uppercase every identifier (table and columns) not
protected by commas. Using Data Brewery, every identifier is protected.
Therefore, when you write a query you might want to either use double quotes like
select "column_name" from ... or decide to use upper case identifiers to avoid confusion.

