

.. _store_google_analytics:

Google Analytics
----------------------------

`Google Analytics <https://analytics.google.com/>`_ is a great way to track what happens on your website.

The datastore specification have the following attributes :

====================== ==========
Parameters             Details 
====================== ==========
name                   how you want this datastore be refered as
type                   should be *googleAnalytics*
viewId                 the identification number of the Google Analytics view (see :ref:`ViewId <store_google_analytics_viewid>`)
serviceAccountEmail    Service account to use (see :ref:`Google Service Account <store_google_service_account>`)
keyFileLocation        The location of the p12 key (see :ref:`Google Service Account <store_google_service_account>`)
====================== ==========


Then, you can define any number of tables with the following attributes :

====================== ==========
Parameters             Details 
====================== ==========
name                   Table name
viewId                 the identification number of the Google Analytics view (see :ref:`ViewId <store_google_analytics_viewid>`)
startDate              Starting date of the timeframe
endDate                End date of the timeframe
====================== ==========

As you can see in the example below, each table 
can be provided a *startDate* and *endDate* field. You can give it in absolute 
with a YYYY-MM-DD pattern or using some special keys like today, yesterday, XdaysAgo (where X is a number).

Then, you can define two types of rows, *dimensions* and *measures* (more details at `Dimensions & Metrics Explorer from Google <https://developers.google.com/analytics/devguides/reporting/core/dimsmets>`_ ). 
Columns *gaName* field correspond to the Google Analytics API name and gaType is either *dimension* or *measure*. 
In case of a measure, *type* is always *bigint*. For a dimension it's *text*.

.. code-block:: xml

   <datastore name="ga" type="googleAnalytics" viewId="123456789">
     <!-- It will import all data from 30 days ago to yesterday -->
     <table name="sessions" startDate="30daysAgo" endDate="yesterday">
       <!-- We start by defining the dimensions (type = text) -->
       <column name="date" type="text" gaName="ga:date" gaType="dimension"/> 
       <column name="medium" type="text" gaName="ga:medium" gaType="dimension"/>
       <column name="source" type="text" gaName="ga:source" gaType="dimension"/>
       <column name="campaign" type="text" gaName="ga:campaign" gaType="dimension"/>
       <column name="country" type="text" gaName="ga:country" gaType="dimension"/>
       <column name="city" type="text" gaName="ga:city" gaType="dimension"/>    
    
       <!-- Then measures (type = bigint) -->
       <column name="sessions" type="bigint" gaName="ga:sessions" gaType="measure"/>
       <column name="bounces" type="bigint" gaName="ga:bounces" gaType="measure"/>
       <column name="pageviews" type="bigint" gaName="ga:pageviews" gaType="measure"/>
     </table>
   </datastore>



.. _store_google_analytics_viewid:

View ID
=========

The *viewId* is the view identifier in Google Analytics, it can be defined at the datastore level 
and/or at the table level. If both are specified, the table one will be choosen.

The *viewId* can be found on the `View Settings <https://analytics.google.com/analytics/web/#management/Settings/>`_ 
on the Admin panel of Google Analytics.


.. image:: /images/google-analytics-viewid.png

You need also to allow `Data Brewery <https://databrewery.co/>`_ to access the data. 
For that, you need to create a :ref:`service account <store_google_service_account>`
and add the service account email to the User Management settings 
(just below the View Settings) with Read & Analyse permissions like below:

.. image:: /images/google-analytics-users.png

