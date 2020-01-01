.. _logging:

Logging
========

Currently, `Data Brewery <https://databrewery.co/>`_ don't provide a specific logging mecanism.
Nevertheless, running the :ref:`ipa CLI <command_line>`, you can control the program verbosity 
with the -v (verbose) and -V (very verbose) parameters and redirect the output to a file.


For instance to redirect the output of the processing of the process *nightly* to the file 
ipa-nightly.log with maximum verbosity, you can use :

.. code-block:: bash

  ipa run-process -V nightly > ipa-nightly.log



