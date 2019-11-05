.. _specs_datastore:

Datastores
============

Datastores are used to describe storage of data. It can be both for input (data sources) and for output (data sinks).


For instance, the following is a datastore describing a PostgreSQL database, with a single table *test* with a single column *a* of type *int* : 

.. code-block:: xml

    <datastore name="mydb" type="postgresql" host="mydb.myproject.com" user="admin" password="mypassword"> 
    	<table name="test">
    		<column name="a" type="int"/>
    	</table>
    </datastore>

You can find the list of supported datastore types :ref:`in the datastores section datastores <datastores>`.