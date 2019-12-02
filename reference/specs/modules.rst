.. _specs_module:

Modules
============

Modules are an unit of data that focus on a single subject with a defined SLA (Service 
Level Agreement), that is coherent at any time with himself. 

The main idea for module came from a blogpost of mine in 2013 arguing that `modules bring
agility <https://dataintoresults.com/post/introducing-the-modular-data-warehouse/>`_ 
which was later extended in a `Agile Data Warehouse Manifesto 
<https://dataintoresults.com/post/data-warehouse-manifesto/>`_.

There is three categories of modules :

* Integration module : A module that duplicate an external data source (staging) or 
  keep and archive of it. See :ref:`replication modules <tuto_replicate>`
* Refinement module : A module that blend integration modules and/or add business rules.
  See :ref:`replication modules <tuto_refine>`
* Presentation module : A module with refined data to be consumed by end users (business
  analysts).

Physically, each module is stored in a specific schema of a database or a repertory in
a case of a data lake.

Each module is attached to a datastore (usually the datawarehouse or a data lake) 
which need to be specified with the attribute *datastore*. It is composed
by tables which contains a *source* element describing how such tables must be
constructed.

By default, each table is recomputed from scratch. Nevertheless, more update 
strategies are available using the *strategy* element. Namely, using the 
*overwrite* strategy, existing data is merged with the result of the source
element using the *businessKey* attribute as a row identification.


.. code-block:: xml

  <!-- Module that replicate the web datastore inside the datawarehouse -->
  <datastore name="staging" datastore="dw"> 
    <replicate datastore="web">
  </datastore>

  <!-- A module that keep track of all sessions data -->
  <datastore name="archive" datastore="dw"> 
    <!-- There will be a row per combination of (date, country) values.
      The last seen value will be used.
      Notice that staging.sessions shouldn't have two rows with the same
      date and country combination. -->
    <table name="sessions" strategy="overwrite" businessKey="date,country">
      <source type="module" module="staging" table="sessions">
    </table>
  </datastore>

  <!-- A module on top of sessions data.
    Notice that this module is recomputed at every run.
    Therefore you can make change without fear of all data -->
  <datastore name="business" datastore="dw"> 
    <table name="sessions">
      <source type="query">
        <!-- probably a more complex query -->
        select * from archive
      </source>
    </table>
  </datastore>



