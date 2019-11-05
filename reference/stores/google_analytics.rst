

.. _integrate_google_analytics:

Google Analytics
----------------------------

`Google Analytics <https://analytics.google.com/>`_ is a great way to track what happens on you front website.

The datastore specification have 3 parameters.

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this data store be refered as
type            should be *googleAnalytics*
viewId          the identification number of the Google Analytisc view (see below)
=============== ==========

The *viewId* can be found on the `View Settings <https://analytics.google.com/analytics/web/#management/Settings/>`_ on the Admin panel of Google Analytics.


.. image:: /images/google-analytics-viewid.png

You need also to allow Datafactory to access the data. For that, add the account *bicloud@bicloud-1.iam.gserviceaccount.com* to the User Management settings (just below the View Settings) with Read & Analyse permissions like below:

.. image:: /images/google-analytics-users.png


The you can define any number of table. As you can see in the example below, each table can be provided a *startDate* and *endDate* field. You can give it in absolute with a YYYY-MM-DD pattern or using some special keys like today, yesterday, XdaysAgo (where X is a number).

Then, you can define two types of rows, dimensions and measures (you can use the `Dimensions & Metrics Explorer from Google <https://developers.google.com/analytics/devguides/reporting/core/dimsmets>`_ ). Columns *gaName* field correspond to the Google Analytics API name and gaType is either *dimension* or *measure*. In case of a measure, *type* is always *bigint*. For a dimension it's *varchar*.

.. code-block:: xml

    <dataWarehouse>
        <!-- Define where the data warehouse is located. -->
        <datastore name="dw" type="postgresql" host="dw.theowner.com" database="datawarehouse" 
            user="john" password="Doe" sshUser="john">
        </datastore>

        <!-- Define access to Google Analytics. -->
        <datastore name="ga" type="googleAnalytics" viewId="123456789">
            <!-- It will import all data from 30 days ago to yesterday -->
            <table name="sessions" startDate="30daysAgo" endDate="yesterday">
                <!-- We start by defining the dimensions (type = varchar) -->
                <column name="date" type="varchar" gaName="ga:date" gaType="dimension"/>    
                <column name="medium" type="varchar" gaName="ga:medium" gaType="dimension"/>    
                <column name="source" type="varchar" gaName="ga:source" gaType="dimension"/>    
                <column name="campaign" type="varchar" gaName="ga:campaign" gaType="dimension"/>    
                <column name="country" type="varchar" gaName="ga:country" gaType="dimension"/>  
                <column name="city" type="varchar" gaName="ga:city" gaType="dimension"/>    

                <!-- Then measures (type = bigint) -->
                <column name="sessions" type="bigint" gaName="ga:sessions" gaType="measure"/>   
                <column name="bounces" type="bigint" gaName="ga:bounces" gaType="measure"/>
                <column name="pageviews" type="bigint" gaName="ga:pageviews" gaType="measure"/>
            </table>
        </datastore>

        <!-- Integration layer module : prestashop  -->
        <!-- In the database it will be store under the m901_prestashop schema  -->
        <module id="902" name="google_analytics" datastore="dw">
            <!-- We replicate all the ga datastore  -->
            <replicate datastore="ga"/>
        </module>     
    </dataWarehouse>                  



After having update your data warehouse specification, you can update it on the DataFactory plateform with  the command :ref:`/datawarehouse command <http_api_datawarehouse_post>` (with dw.xml being your data warehouse specification):

.. code-block:: console

    curl -X POST -u token:<your_token> -H "Content-Type: text/xml" --data-binary "@dw.xml" https://etl.dataintoresults.com/api/v0/datawarehouse

You can now ask DataFactory to run your refining module with the :ref:`/run/module command <http_api_run_module>` (in this case the module id is *902*):

.. code-block:: console

    curl -u token:<your_token> https://etl.dataintoresults.com/api/v0/run/module/902

