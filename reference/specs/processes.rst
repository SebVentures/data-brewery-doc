.. _spec_process:

Processes
============


Processes are a succession of task that must be run in order. Each task is run 
sequentially except if an error is raised previously. In such case, the rest of the tasks are 
skipped.

Process parameters are the following :

=================== =====================
Parameter           Description
=================== =====================
name                Name of the process
email               Emails (separated by comma) to send a report at the end of the process
                    (see :ref:`emails notifications <notification_email>`)
emailWhen           Process outcomes (separated by comma) when to send an email.
                    By default : success,warning,error (see :ref:`emails notifications <notification_email>`)
slack               Slack webhook to send a post at the end of the process (see :ref:`Slack notifications <notification_slack>`)
slackWhen           Process outcomes (separated by comma) when to send an Slack post.
                    By default : success,warning,error (see :ref:`Slack notifications <notification_slack>`)
=================== =====================

A process can end with the following status : 

- **success :** all task finished with as success 
- **warning :** at last one stask finished with a warning and none with an error
- **error :** at least one stask finished with an error

Tasks
------

Task are elements (or steps) of a process (and can be defined only inside a process element).
A task can succeed or it can fail with an error. However, there is a onError attribute to modify the outcome.

Process parameters are the following :

=================== =====================
Parameter           Description
=================== =====================
name                Name of the task. This is optional. If not given it will take a name depending on the task type.
type                Type of the task (see below)
onError             How should we treat an error in this task. Possible values are : error (default), warning and success
=================== =====================


A process can end with the following status : 

- **success :** all task finished with as success 
- **warning :** at last one stask finished with a warning and none with an error
- **error :** at least one stask finished with an error
- **unprocessed :** the tasks was not processed because an upstream task ended with an error


The possible task types are :

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
    <task name="module_a" module="a"/>
    <!-- name of this task will be b -->
    <task module="b"/>
    <!-- name of this task will be c and, if it fail, its status will be warning instead of error, the process will continue -->
    <task datastore="c" onError="warning"/>
    <!-- name of this task will be shell/d -->
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

