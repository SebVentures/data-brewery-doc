
.. _store_oracle:

Oracle
--------------------------

`Oracle <https://www.oracle.com/database/>`_ is one of the oldest SQL 
database around.

There is some significant changes from other SQL databases:

- The date type in Oracle contains hours, minutes and seconds. Therefore,
  we treat date as datetime.
- The type system is (see :ref:`type handling <store_oracle_types>`).
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



.. _store_oracle_types:

About types
=============

The type system in Oracle is quite different from other databases. You will find below
some notes about how the mapping between Oracle and Data Brewery is managed.

Numbers
########

The main type for reprensenting numbers is *NUMBER*. It is used to reprensent integers
as well as decimal values. *NUMBER(p, d)* is such that *p* is the number of significant 
allowed digits and *d* is the number of decimals.
Oracle types *NUMBER(x,0)* is recognized as *int* when *x* is strictly below 9 and as 
*bigint* when *x* is strictly below 19. On the other hand, *int* is persisted as 
*NUMBER(9,0)* and *bigint* as *NUMBER(19,0)*. Reading back those values will result to
*bigint* and *numeric*.
Otherwise, *NUMBER* is mapped as the *numeric* type.

For floating precision numbers (Data Brewery type *double*), Oracle provide 
*BINARY_FLOAT* and *BINARY_DOUBLE* since the version 10. *FLOAT*,
*DOUBLE PRECISION* and other similar types are in fact just an alias for
*NUMBER* (fixed precision).
We recognize *BINARY_FLOAT* and *BINARY_DOUBLE* as *double*. The
*double* type is persisted as *BINARY_DOUBLE*.

Text
########

*text* is mapped to *VARCHAR2(256)* and *bigtext* is mapped to *CLOB*. 
*VARCHAR2(x)* columns are mapped to *text* when they are below or equals 256,
*bigtext* when above. *CLOB* is always mapped to *bigtext*.


.. _store_oracle_case_handling:

Case handling
=============

By default, Oracle uppercase every identifier (table and columns) not
protected by commas. Using Data Brewery, every identifier is protected.
Therefore, when you write a query you might want to either use double quotes like
select "column_name" from ... or decide to use upper case identifiers to avoid confusion.

