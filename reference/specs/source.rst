.. _specs_datastore:

Sources
============

Source can be used for tables that lies in a :ref:`datastore <spec_datastore>` 
or a :ref:`module <spec_module>`. It provide information on how to
fill the table.

The types of sources are described in the table below :

================== ============
Source type        Description
================== ============
query              Execute a SQL query inside the :ref:`module <spec_module>` 
                   or :ref:`datastore <spec_datastore>` of te table. Only work
                   from SQL datastores. No additional paramaters.
datastoreQuery     Execute a SQL query in the given :ref:`datastore <spec_datastore>`
                   (which should be an SQL datastore). The *datastore* parameter
                   specify which datastore to use.
datastore          Reference a table inside a datastore. The *datastore* parameter
                   specify which datastore to use and the *table* parameter specify 
                   which table inside the *datastore* to use.
module             Reference a table inside a module. The *module* parameter
                   specify which module to use and the *table* parameter specify 
                   which table inside the *module* to use.
================== ============



