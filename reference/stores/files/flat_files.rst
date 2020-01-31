
.. _store_flat_files:

Flat Files
-----------

Flat files regroup all files that lies under your local filesystem. 
This connector can read and/or write (see :ref:`writing <store_flat_files_csv_writing>`) 
to such files.
Currently, :ref:`CSV files <store_flat_files_csv>` and :ref:`XLSX files <store_flat_files_xlsx>` are supported.


Parameters on the datastore element are the following :

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this data store be refered as
type            should be *file*
location        root location for all tables of this datastore. 
                This parameter is optionnal
=============== ==========

.. _store_flat_files_csv:

CSV files
=========

A CSV tables has the following parameters

==================== ==========
Parameters           Details 
==================== ==========
name                 how you want this table be refered as
type                 should be *csv*
location             location of the file (or files, see :ref:`pattern reference <store_flat_files_csv_pattern>`)
locationSeparator    separator for *location* (see :ref:`pattern reference <store_flat_files_csv_pattern>`)
locale               encoding to use for the file. By default it is "UTF-8" (more information 
                     `here <https://docs.oracle.com/javase/8/docs/api/java/nio/charset/Charset.html>`_)
compression          compression format to use. Can be none (default, no compression) 
                     or gz (for gzip compression)
delimiter            separator for cells (default tabulation, "\t")
header               is there an header (first row) : true (default) or false
newline              character to use to specify a new line (default "\n")
quote                character to use that quote cells (default ", specified by "\"")
quoteEscape          character to use before a quote character inside a cell that isn't 
                     a quote (default " specified by "\"")
comment              character which at the begining of a line indicate that it should be skipped
==================== ==========


.. _store_flat_files_csv_pattern:

File location patterns
######################

Each CSV table can be composed of many CSV files by using the "*" character in 
the location. "*" means any value

For instance the following description will import any file which is in the
pattern /data/year=\*/month=\*/file-\*.csv

.. code-block:: xml

  <datastore name="flat" type="flat" location="/data/">
    <!-- File that will be used as source -->
    <table name="src" type="csv" location="year=*/month=*/file-*.csv">
      <column name="col1" type="text"/>
    </table>
  </datastore>

Therefore :

* /data/year=2019/month=11/file-20191101.csv : will be taken
* /data/year=2019/month=11/day=01/file-20191101.csv : will be NOT be taken (one directory)
* /data/year=2019/month=11/notafile.csv : will be NOT be taken (not a good file name)


Additionnaly, the *location* attribute at the table level can store multiple locations.
Each location is separated by the pipe character ('|') but you can change the separator
using the *locationSeparator* attribute.

You can mix multiple locations and the patterns.

For instance, the following example will read every csv files in the repertory /data/a/ and
all csv files in the repertory /data/b/ (but not in the repertory /data/c/ for instance).

.. code-block:: xml

  <datastore name="flat" type="flat" location="/data/">
    <!-- File that will be used as source -->
    <table name="src" type="csv" location="a/*.csv|b/*.csv">
      <column name="col1" type="text"/>
    </table>
  </datastore>


.. _store_flat_files_csv_writing:

Writing to CSV files
######################

You can write to a CSV file by providing a source element to the table that describe 
how it must be filled.


When a CSV table is used for ouput only, it is not needed to supply the columns
structure. If absent, the structure of the feeding stream will be used.


.. code-block:: xml

  <datastore name="flat" type="file" location="/data/">
    <!-- File that will be used as source -->
    <table name="src" type="csv" location="input.csv">
      <column name="col1" type="text"/>
    </table>

    <!-- File that will contain a copy of the src table -->
    <table name="dest1" type="csv" location="output.csv">
      <!-- If the table is in a module, just change datastore by module -->
      <source type="datastore" datastore="flat" table="src"/>
    </table>

    <!-- File that will contain the output of a SQL query -->
    <table name="dest2" type="csv" location="output-sql.csv">
      <!-- queries only work on a SQL datastore -->
      <source type="datastoreQuery" datastore="dw">
        select * from my_table where col1 = 'A'
      </source>
    </table>
  </datastore>

To process the datastore (all tables with a source element), you can use the following 
command : 

.. code-block:: bash

  ipa run-datastore flat

It will produce the output.csv and output-sql.csv files.


.. _store_flat_files_xlsx:

Excel files (XLSX only)
=========================


An Excel tables has the following parameters. You can read or write from an Excel file.

================== ==========
Parameters          Details 
================== ==========
name               how you want this table be refered as
type               should be *xlsx*
location           location of the file (or files, works like CSV files, see :ref:`pattern reference <store_flat_files_csv_pattern>`)
locationSeparator  separator for *location* (works like CSV files, see :ref:`pattern reference <store_flat_files_csv_pattern>`)
sheet              Which sheet in the file should be used
colStart           Column index of the first column (start with A)
rowStart           Row number where data should be read/written (start at 1, header should be excluded)
================== ==========

Currently, there is also metadata discovery, meaning you need to specify the column names
and types you want to read from the Excel file. If a header is present in the file,
it shouldn't be used (*rowStart* should be after the header row).

On Windows, you can't write to an Excel file which is already open in Excel.

The following example shows a datastore named excel that acces files in the directory data.
Two tables are defined

.. code-block:: xml

  <datastore name="excel" type="file" location="./data/">
    <!-- File that will be used as source -->
    <table name="src" type="xlsx" location="input.xlsx"
      sheet="input_sheet" rowStart="2" colStart="A">
      <column name="col1" type="text"/>
      <column name="col1" type="datetime"/>
    </table> 
    <!-- File that will be used as output (no need to provide columns/metadata) -->
    <table name="src" type="xlsx" location="input.xlsx"
      sheet="output_sheet" rowStart="2" colStart="A">
      <source type="datastore" datastore="excel" table="src"/>
    </table>
  </datastore>



