
.. _store_http:

Http Files
-----------

The Http connector allows to read files on the web using either http or https protocol.
Files should be small enough to fit memory. The connector can parse either text files,
CSV files or HTML files. For the later, one should provide a xpath to locate each column 
(more precisely a jsoup selector, see `here or more information 
<https://jsoup.org/cookbook/extracting-data/selector-syntax>`_). 

Http files can't be used for output.


Parameters on the datastore element are the following :

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this data store be refered as
type            should be *http*
=============== ==========


Parameters on the table element are the following :

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this table be refered as
location        Web location for the file
format          should be text, csv or html
csvSeparator    (CSV only), which cell separator to use (default ",")
csvQuote        (CSV only), how to quote cells (default ", escaped with "\"")
csvHeader       (CSV only), is there an header: true (default) or false
=============== ==========

For the html format, there is also a new parameter for the underlying *column* elements.


=============== ==========
Parameters      Details 
=============== ==========
path            how to finds the cells for the column, use `JSOUP selectors
                <https://jsoup.org/cookbook/extracting-data/selector-syntax>`_ 
=============== ==========


Examples
========

.. code-block:: xml

  <datastore name="web" type="http">
    <!-- Example reading a csv file located on the web -->
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

    <!-- Example reading a text file located on the web -->
    <table name="iso_8859_1" location="https://www.w3.org/TR/PNG/iso_8859-1.txt" format="text">
      <column name="row" type="bigtext"/>
    </table>

    <!-- Example reading a web page and extracting two point of data -->
    <table name="spy" location="https://finance.yahoo.com/quote/SP?p=SP"
      format="html">
      <column name="last_close" type="numeric" path="td[data-test=PREV_CLOSE-value]"/>
      <column name="pe_ratio" type="numeric" path="td[data-test=PE_RATIO-value]"/>
    </table>
  </datastore>


