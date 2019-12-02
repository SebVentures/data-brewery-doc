.. _spec_process:

Processes
============


Processes are a succession of task that must be run in order.

The possible task are :

=================== =====================
Task type           Description
=================== =====================
module              Process a specific module (attribute module)
datastore           Process a datastore (see :ref:`processable datastores <processable_datastores>`)
shellCommand        A shell command to be run (see :ref:`shell commands <shell_command>`)
=================== =====================

For instance, the following chunk of code define a process that 
process two modules (a and b) then datastore c
and finally run the program shell/d.bat.

.. code-block:: xml

  <process name="datastore_process">
    <task module="a"/>
    <task module="b"/>
    <task datastore="c"/>
    <task shellCommand="shell/d"/>
  </process>


.. _processable_datastores:

Processable datastores
----------------------

Some datastore can be processed (in fact all can be processed, 
but most don't have anything be processed). What is processed in a 
datastore is *table* elements with a *source* sub-element like the 
following :


.. code-block:: xml

    <datastore name="mydb" type="postgresql" host="mydb.myproject.com" user="admin" password="mypassword"> 
      <table name="test">
        <source type="datastoreQuery" datastore="dw">
          select * from business.analysis
        </source>
      </table>
    </datastore>

This datastore define a table which source an sql query from another 
datastore (hence the *datastoreQuery* type). The *datastore* attribute
define the datastore where the inside SQL query must be run.

Should the query be run inside the table datastore, the *query* type
can be used (no need for a datastore attribute).

To process such datastore you can create a process like : 

.. code-block:: xml

  <process name="datastore_process">
    <task datastore="mydb">
    </task>
  </process>


.. _shell_command:

Shell commands
--------------

The *shellCommand* task execute an external program, for instance the external-program
in the code below.

.. code-block:: xml

  <process name="shell_process">
    <task shellCommand="external-program">
      <parameter value="${conf.dw.process.shell_process.param_1}"/>
    </task>
  </process>


The code is executed in the current root directory (where the program *ipa* is 
executed). 

You can pass parameters with *parameter* sub-elements. There can be many parameters
and the order is conserved. You can reference configuration parameters (see 
:ref:`configuration files <separated_configuration>`) by using ${..}.

By convention, shell programs are located under the /shell directory inside a project.

