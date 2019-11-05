.. _refine:

Refine
=======================================

After having your integration layer set up, time is now to refine all those raw data to create insight.

It's quite easy, you define a module, and then the table you want to create and how you want them to be created. Currently, you can only define SQL queries as shown below.

Each query is enclosed by <![CDATA[ and ]]> to avoid any character to be misinterpreted with a XML meaning.

.. code-block:: xml

    <dataWarehouse>
        <!-- Data stores and integration modules not included. -->

        <!-- This is a refining module, by convention you can use anything from 2xx to 8xx as id) -->
        <module id="401" name="reporting" datastore="dw">
            <!-- We define a table -->
            <table name="customer">
                <!-- Notice we skip the columns definition. We take what the query will give us -->
                <!-- The query is plain SQL and tell DataFactory how to fill the table -->
                <source type="query">
                    <![CDATA[
    SELECT id_customer, email, count(1) as nb_orders, min(newsletter_date_add) as newsletter_add, min(invoice_date) as first_invoice_date, 
        max(invoice_date) as last_invoice_date, sum(total_paid) as total_revenues, min(total_paid) as smaller_invoice, max(total_paid) as larger_invoice, min(birthday) as birthday
    FROM m901_prestashop.ps_orders
    inner join m901_prestashop.ps_customer using (id_customer)
    where email not in ('pub@prestashop.com', 'bob@theowner.com', 'qa@theowner.com' )
        and payment <> 'Commande gratuite'
    group by 1, 2
    order by 2 desc]]>
                </source>
            </table>
            <table name="invoice">
                <source type="query">
                <!-- As you can see we can reference of this module, but only if there are defined above 
                the table in the specification -->
                    <![CDATA[
    SELECT id_order, id_customer, invoice_date, case when invoice_date = first_invoice_date then 1 else 0 end as new_customer, 1 as nb_transactions, total_paid as revenues
    FROM m901_prestashop.ps_orders o
    inner join m901_prestashop.ps_customer using (id_customer)
    inner join m401_legacy.customer c using( id_customer)
    where payment <> 'Commande gratuite';]]>
                </source>
            </table>
        </module>
    </dataWarehouse>                     




After having update your data warehouse specification, you can update it on the DataFactory plateform with  the command :ref:`/datawarehouse command <http_api_datawarehouse_post>` (with dw.xml being your data warehouse specification):

.. code-block:: console

    curl -X POST -u token:<your_token> -H "Content-Type: text/xml" --data-binary "@dw.xml" https://etl.dataintoresults.com/api/v0/datawarehouse

You can now ask DataFactory to run your refining module with the :ref:`/run/module command <http_api_run_module>` (in this case the module id is *401*):

.. code-block:: console

    curl -u token:<your_token> https://etl.dataintoresults.com/api/v0/run/module/401

