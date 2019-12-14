.. _tuto_refine:

Refine the data 
-----------------

The purpose of a data warehouse is to archive data and 
refine it in order ease usage. Refining can be 
blending of multiple data sources, fixing some
data source issues and/or apply some business rules.

Refining can also be modeling the data using a star schema 
to make it faster to manipulate and easier to understand.

For this tutorial, we will just define some tables
that will be used for reporting later. SQL queries will therefore
stay simple, basically some non-complex SQL queries. 

All those tables will be stored in the :ref:`module <specs_module>`
earthquake stored in the datawarehouse declared previously.

.. code-block:: xml

  <module name="earthquake" datastore="dw"> 
    <!-- module tables goes here -->
  </modules>

For instance, the *daily_stats* table aggregate earthquakes by day,
over the last 30 days, and count the number of earthquake, find
the maimal and average earthquake magnitude. I.e, the following
query :

.. code-block:: sql

  select cast("time" as date) as "date", count(*) as "earthquake_count", max("mag") as "max_magnitude",
    avg("mag") as "avg_magnitude" 
  from "archive_web"."earthquake"
  group by cast("time" as date)
  order by cast("time" as date) desc
  limit 30

This query will be embedded in a :ref:`:source element <spec_source>` which has a type *query*.

You can find below the full specification of the module earthquake.

.. code-block:: xml

  <!-- Model some (simple) stats tables for reporting -->
  <module name="earthquake" datastore="dw"> 
    <table name="daily_stats">
      <source type="query">
        select cast("time" as date) as "date", count(*) as "earthquake_count", max("mag") as "max_magnitude",
          avg("mag") as "avg_magnitude" 
        from "archive_web"."earthquake"
        group by cast("time" as date)
        order by cast("time" as date) desc
        limit 30
      </source>
    </table>

    <table name="weekly_stats">
      <source type="query">
        select to_char(cast("time" as date), 'IYYY-IW') as "date", count(*) as "earthquake_count", max("mag") as "max_magnitude",
          avg("mag") as "avg_magnitude" 
        from "archive_web"."earthquake"
        group by to_char(cast("time" as date), 'IYYY-IW')
        order by to_char(cast("time" as date), 'IYYY-IW') desc
        limit 14
      </source>
    </table>

    <table name="last_big_ones">
      <source type="query">
        select "time", "mag", "latitude", "longitude", "place"
        from "archive_web"."earthquake"
        order by "mag" desc
        limit 7
      </source>
    </table>

    <table name="mag_vs_depth_corr" type="xlsx" location="earthquake.xlsx"
      sheet="DATA - mag vs depth" colStart="A" rowStart="2">
      <source type="query">
        select "mag", "depth"
        from "archive_web"."earthquake"
        order by "time" desc
        limit 60
      </source>
    </table>
  </module>


Now that the data is prepared for reporting, we can get to the :ref:`reporting phase <tuto_report>`.


