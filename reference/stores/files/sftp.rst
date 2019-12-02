
.. _store_sftp:

SFTP Files
----------


The SFTP connectors allows to read a CSV file accross the SFTP protocol. 
Currently, the file should be small enough to fit memory and all columns
should be text. It is currently not possible to write to an sftp file.

Parameters on the datastore element are the following :

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this data store be refered as
type            should be *sftp*
host            host of the SFTP server
user            user login
password        password to use (can't use sshkeys yet)
path            parent path for the tables (by default "/")
=============== ==========


Parameters on the table element are the following :

=============== ==========
Parameters      Details 
=============== ==========
name            how you want this table be refered as
filePattern     path relative to the path given in the datastore.
fileFormat      should be csv
csvSeparator    how fields are separated in the file (default tabulation, i.e. "\t")
=============== ==========

Example
=======

.. code-block:: xml

  <datastore name="sftp" type="sftp" path="/home/john/" host="mysftpserver.com" user="john" password="doe">
    <table name="test" filePattern="test.csv" fileFormat="csv" csvSeparator="\t">
      <column name="col1" type="text"/>
      <column name="col2" type="text"/>
    </table>
  </datastore>


