.. _integrate:

Integrate
===========

The first issue you face when you work with multiple on premise software and cloud-based software is that all those data are not in the same place and not in the same database. Therefore, the first step is to get everything in the same place: the data warehouse. DataFactory can help you with that as a good number of connectors are already designed to do exactly that. You specify where the data lies, and DataFactory will get it for you.


In every case, you need 3 parts in your :ref:`data warehouse specification <specification>`:

 - A data store to host the data warehouse (*dw* in the sample below) 
 - A data store to integrate data from (*source* in the sample below)
 - A replication module for the integration (module with id 901 in the sample below)


.. code-block:: xml

    <dataWarehouse>
        <!-- Define where the data warehouse is located. -->
        <datastore name="dw"/>

        <!-- Define the source to integrate in the data warehouse and it's type. -->
        <datastore name="source" type="..." />

        <!--  The replication module (id using the pattern 9xx  by convention). -->
        <module id="901" name="..." datastore="dw">
            <!-- We replicate all the "source" datastore-->
            <replicate datastore="source"/>
        </module>
    </dataWarehouse>                     

As a convention, we use the pattern 9xx for integration modules.


.. toctree::
   :maxdepth: 1
   :caption: Integrations

   tech/integration/sql
   tech/integration/google_analytics
   tech/integration/google_sheet




Now that you've integrate external data sources in your data warehouse, it's time to blend them and extract business value. :ref:`Learn more about refining modules <refine>` to continue your journey.
