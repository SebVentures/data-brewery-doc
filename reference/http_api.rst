.. _http_api:

HTTP API
========

When the command span on multiple lines, we use \\ (Linux version). On Windows, you should use ^ instead.

/account POST 
--------------------

Create a new account with the mail in the body of the POST request (field = email).

.. code-block:: bash

  curl --data "email=john@doe.com" https://etl.dataintoresults.com/api/v0/account

The service will send you an email with a token in your mailbox. The token is a string of 64 characters. With it you can check the specification of your data warehouse.

/datawarehouse GET 
------------------

Return the [datawarehouse specification](specification.md) in XML.

.. code-block:: bash

  curl -u token:<your_key> https://etl.dataintoresults.com/api/v0/datawarehouse


.. _http_api_datawarehouse_post:

/datawarehouse POST 
-------------------

Set a new specification for the datawarehouse. The body should be a XML compliant [datawarehouse specification](specification.md).

.. code-block:: bash

  curl -X POST -u token:<your_key> -H "Content-Type: text/xml" --data "@dw.xml" \
  	https://etl.dataintoresults.com/api/v0/datawarehouse


.. _http_api_job_running:

/job/running GET
----------------

Returns the currently runninh jobs for the user. Jobs are all API call except /job/running and /job/history


=============== ============
Column          Details
=============== ============
event_log_id    A number representing the job/event
module          The part of the software used, currently always "factory"
event           The job
details         A JSON object giving more information (event specific)
start_timestamp When the job call started
running_time    Since how many seconds the job is running
=============== ============



event_log_id, module, event, details, start_timestamp, running_time

.. code-block:: bash

	curl -u token:<your_key> -H "Accept: text/plain" \
		https://etl.dataintoresults.com/api/v0/job/running


.. _http_api_job_history:

/job/history GET
----------------

Returns all the jobs that were run for the user. Jobs are all API call except /job/running and /job/history.

event_log_id, module, event, status, details, issue, start_timestamp, end_timestamp, running_time

=============== ============
Column          Details
=============== ============
event_log_id    A number representing the job/event
module          The part of the software used, currently always "factory"
event           The job
status          FAILED or SUCCESS
details         A JSON object giving more information (event specific)
issue           A JSON object giving more information if status = FAILED (issue specific, at least an "issue" field)
start_timestamp When the job call started
end_timestamp   When the job stopped
running_time    How many seconds the job was running
=============== ============



.. code-block:: bash

	curl -u token:<your_key> -H "Accept: text/plain" \
		https://etl.dataintoresults.com/api/v0/job/history



.. _http_api_table_data:

/table/<datastore>/<table>/data GET
-----------------------------------

Read data from the table <table> in the datastore <datastore> given in the URL. Currently it returns 100 rows.

You can choose formatting using the Accept in the HTTP header.


======================= =====================
formatting              Details
======================= =====================
text/plain              Coma separated values with a header
application/stream+json A new JSON every row on a new line
application/json        One big JSON object
application/xml         Data in XML.
======================= =====================


.. code-block:: bash

	curl -u token:<your_key> -H "Accept: <formatting>" \
		https://etl.dataintoresults.com/api/v0/<datastore>/<table>/data


.. _http_api_run_replicate_ds:

/run/replicate/<dsFrom>/<tabFrom>/<dsTo>/<tabTo> GET
----------------------------------------------------

Copy the table <tabFrom> from data store <dsFrom> to the table <tabTo> in the data store <dsTo>. Both table should exist in the specification. The destination table will be created in the database if it doesn't exists. If it exists, the table will be dropped then created.

.. code-block:: bash

	curl -u token:<your_key> \
		https://etl.dataintoresults.com/api/v0/run/replicate/<dsFrom>/<tabFrom>/<dsTo>/<tabTo>

.. _http_api_run_module:

/run/module/<module_id> GET
---------------------------

Refresh the module <module_id> given in the URL.


.. code-block:: bash

	curl -u token:<your_key> \
		https://etl.dataintoresults.com/api/v0/run/module/<module_id>

