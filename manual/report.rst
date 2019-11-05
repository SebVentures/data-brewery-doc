.. _tuto_report:

Create a dashboard
------------------

.. code-block:: xml

    <dataWarehouse>
        <datastore name="web" type="http">
                <table name="earthquake" endpoint="https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_day.csv" format="csv" csvHeader="true">
                    <column name="time" type="datetime" temporalFormat="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"/>
                    <column name="latitude" type="numeric"/>
                    <column name="longitude" type="numeric"/>
                    <column name="depth" type="numeric"/>
                    <column name="mag" type="numeric"/>
                    <column name="magType" type="varchar"/>
                    <column name="nst" type="numeric"/>
                    <column name="gap" type="numeric"/>
                    <column name="dmin" type="numeric"/>
                    <column name="rms" type="numeric"/>
                    <column name="net" type="varchar"/>
                    <column name="id" type="varchar"/>
                    <column name="update" type="datetime" temporalFormat="yyyy-MM-dd'T'HH:mm:ss.SSS'Z'"/>
                    <column name="place" type="varchar"/>
                    <column name="type" type="varchar"/>
                    <column name="horizontalError" type="numeric"/>
                    <column name="depthError" type="numeric"/>
                    <column name="magError" type="numeric"/>
                    <column name="magNst" type="numeric"/>
                    <column name="status" type="varchar"/>
                    <column name="locationSource" type="varchar"/>
                    <column name="magSource" type="varchar"/>
            </table>
        </datastore>
    </dataWarehouse>