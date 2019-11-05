.. _structure:

Project structure
=================

In `Data Brewery <https://databrewery.co/>`_ your data environment is described 
by a set of XML files and some `HOCOM (Human-Optimized Config Object Notation) 
files <https://github.com/lightbend/config/blob/master/HOCON.md#hocon-human-
optimized-config-object-notation>`_ (think JSON on steroid).

In its simplest form, you can have the following file dw.xml that simply 
define a data source of earthquake data.


.. code-block:: xml

    <datawarehouse>
        <datastore name="web" type="http">
            <table name="earthquake" location="https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.csv" 
                format="csv" csvHeader="true">
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
    </datawarehouse>


. It is based on our :doc:`Data Manifesto <../manifesto>`. You can download it `here <https://dataintoresults.com/data-tool.html>`_, all you need is Java 8 or above.

It's a command line tool 


.. image:: ../images/baton-help.png
   :align: center
