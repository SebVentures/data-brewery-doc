.. _command_line:

Command line - IPA CLI
=======================

You can operate a `Data Brewery <https://databrewery.co/>`_ data warehouse with 
the **ipa** CLI (command line interface). See :ref:`how to install Data Brewery <data_tool>` if needed

You can find the help by just launching the program.

.. code-block:: text

  ipa 1.0.0-M2 20191219
  Usage: ipa [init|read|run-module|run-datastore|run-process] [options] <args>...

    -i, --dw <dwFile>        Datawarehouse XML definition file to use (default dw.xml)
    -c, --conf <confFile1>,<confFile2>
                            Configuration files to use (always read dw.conf first).
    -s, --silent             Disable logging messages.
    -v, --verbose            Display more logging messages.
    -V, --very-verbose       Display even more logging messages.
    --help                   prints this usage text

  Command: init [<seed>]
  init create a new data projet.
    <seed>                   Optionnal : Use a seed to base project (accept : simple, complex)

  Command: read [options] <table>
  read the content of a table.
    <table>                  the table to read
    --nb-rows <value>        Number of rows to read (10 if not set).
    --no-limit               Read an unlimited number of rows.

  Command: run-module <module>
  run-module process the specified module.
    <module>                 module(s) to be processed (required)

  Command: run-datastore <datastore>
  run-datastore process the specified datastore.
    <datastore>              the datastore to be processed (required)

  Command: run-process <process>
  run-process launch the specified process.
    <process>                the process to be launched (required)

