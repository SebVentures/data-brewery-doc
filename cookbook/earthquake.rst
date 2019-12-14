.. _cookbook_earthquake:

Earthquake
------------------------

.. code-block:: xml

  <datawarehouse>
    <datastore name="dw" type="h2"  host="file" database="./h2dw"/>

    <datastore name="web" type="http">
      <table name="earthquake" location="https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.csv" format="csv" csvHeader="true">
        <column name="time" type="datetime" temporalFormat="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"/>
        <column name="latitude" type="numeric"/>
        <column name="longitude" type="numeric"/>
        <column name="depth" type="numeric"/>
        <column name="mag" type="numeric"/>
        <column name="magType" type="text"/>
        <column name="nst" type="numeric"/>
        <column name="gap" type="numeric"/>
        <column name="dmin" type="numeric"/>
        <column name="rms" type="numeric"/>
        <column name="net" type="text"/>
        <column name="id" type="text"/>
        <column name="update" type="datetime" temporalFormat="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"/>
        <column name="place" type="text"/>
        <column name="type" type="text"/>
        <column name="horizontalError" type="numeric"/>
        <column name="depthError" type="numeric"/>
        <column name="magError" type="numeric"/>
        <column name="magNst" type="numeric"/>
        <column name="status" type="text"/>
        <column name="locationSource" type="text"/>
        <column name="magSource" type="text"/>
      </table>      
    </datastore>

    <!-- This module just copy the web.earthquake data source -->
    <module name="staging_web" datastore="dw"> 
      <table name="earthquake">
        <source type="datastore" datastore="web" table="earthquake"/>
      </table>
    </module>

    <!-- Output the earthquakes -->
    <datastore name="output" type="flat" location="./">
      <table name="earthquake" type="csv" compression="gz" location="output-sql.csv.gz">
        <source type="datastoreQuery" datastore="dw">
          select * from "staging_web"."earthquake"
        </source>
        <!-- could have use
        <source type="datastore" datastore="web" table="earthquake"/>
        and not use H2 dw at all.
        -->
      </table>
    </datastore>

    <process name="process">
      <task module="staging_web"/>
      <task datastore="output"/>
    </process>
  </datawarehouse>



.. code-block:: bash

  ipa run-process process
