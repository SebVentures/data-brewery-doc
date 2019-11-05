
.. _integrate_google_sheet:

Google Sheet
----------------------------


`Google Sheet <https://www.google.fr/intl/fr/sheets/about>`_ is convenient to store user generated data like reference data or manual sales tracking.


The datastore specification have 2 parameters. There is not much information here.

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this data store be refered as
type            should be *googleSheet*
=============== ==========

Details are provided at table level, where every table of the datastore link to a selection inside a Google Sheet.

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this table be refered as
spreadsheetId   the id of the Google Spreadsheet
sheet           the sheet name of a workbook (optionnal if it's the main sheet)
colStart        the column of the upper left cell of the selection (example "A" or "BF")
rowStart        the row of the upper left cell of the selection (exemple "1" or "3")
=============== ==========


The spreadsheetId for a Google Sheet can be found in the URL of the workbook like below (the spreadsheet can be consulted `here <https://docs.google.com/spreadsheets/d/1yTbUOzUzWvfKz-cJ2Oua6hGU1Li45RFzemzEZEzDfQg/edit#gid=0>`). This workbook has only one sheet that is named "DATA". Therefore, using sheet="DATA" is optionnal.

.. image:: images/google_sheet_spreadsheetid.jpg
   :align: center

In this example, the upper left cell of the selection (sourrounded in red) is A2. We don't use the header row (it is therefore not needed). The name, type and the number of columns are defined within the table specification with columns elements that have two attributes: the name of the column and its type.


.. code-block:: xml

    <dataWarehouse>
        <datastore name="google_sheet" type="googleSheet">
            <table name="earthquake" spreadsheetId="1yTbUOzUzWvfKz-cJ2Oua6hGU1Li45RFzemzEZEzDfQg" sheet="DATA" colStart="A" rowStart="2">
                <column name="time" type="varchar"/>
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
                <column name="update" type="varchar"/>
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


After having update your data warehouse specification, you can update it on the DataFactory plateform with the command :ref:`/datawarehouse command <http_api_datawarehouse_post>` (with dw.xml being your data warehouse specification):

.. code-block:: console

    curl -X POST -u token:<your_token> -H "Content-Type: text/xml" --data-binary "@dw.xml" https://etl.dataintoresults.com/api/v0/datawarehouse


You can now read the data from this Google Sheet using the :ref:`/table/data command <http_api_table_data>`.

.. code-block:: console

    curl -u token:<your_token> https://etl.dataintoresults.com/api/v0/table/google_sheet/earthquake/data

