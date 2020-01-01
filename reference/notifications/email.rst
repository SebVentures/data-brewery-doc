.. _notification_email:

Emails notifications
==========================



Emails notifications enable `Data Brewery <https://databrewery.co/>`_ to send an email at the
end of processes. The recipients can be set with the *email* attribute on the *process* element.
You can set multiple emails by separating them with comma).
You can defined on which events a mail should be sent using the *emailWhen* attribute. It 
could contain : success, warning and/or error (you can select many event by separating them 
with comma). For more detail, see :ref:`the process page <spec_process>`.


It is recommanded to set those parameters in the configuration files. Indeed, it's most likely
that you want notification only for the production environment (see 
:ref:`environment configuration <environment_configuration>` for more details).



Setting SMTP server connection
-------------------------------

In order to get emails working, you need to configure a SMTP server that will relay the mail.
Such configuration should be done in a configuration file (dw.conf by example). It is stored
under the dw.mailer path. You can find more information on the 
`Play Mailer site <https://github.com/playframework/play-mailer>`_ (which is used to send emails).

For instance, below is an exemple to use you GMail account to send email.

.. code-block:: json

  dw.mailer {
    host = "smtp.gmail.com" // (mandatory)
    port = 587 // (defaults to 25)
    ssl = no // (defaults to no)
    tls = yes // (defaults to no)
    tlsRequired = no // (defaults to no)
    user = "your_account@gmail.com" // (optional)
    password = "your_password" // (optional)
    debug = no // (defaults to no, to take effect you also need to set the log level to "DEBUG" for the application logger)
    timeout = null // (defaults to 60s in milliseconds)
    connectiontimeout = null // (defaults to 60s in milliseconds)
    mock = no // (defaults to no, will only log all the email properties instead of sending an email)
    props = {}
  }
  

Example
----------

As discussed at the begining of this page, it is better to specify the email notification 
configuration in a configuration file. Therefore, the XML specification will not 
show any sign of emails.

.. code-block:: xml

  <process name="liveEmail">
    <task module="staging_web"/>
    <task module="nok2" onError="warning"/>
    <task module="archive_web"/>
    <task module="earthquake"/>
    <task name="failingTask" module="nok2"/>
    <task module="ok2"/>
  </process>

But in the configuration file we will add the necessary parts :

.. code-block:: json

  dw.process.liveEmail.email = "seb@databrewery.co,data@databrewery.co"
  dw.process.liveEmail.emailWhen = "error,warning"


You can see below an exemple of email received for the process *liveEmail* when it
ends with an error.

.. image:: ../../images/data-brewery-mail.png
   :align: center

