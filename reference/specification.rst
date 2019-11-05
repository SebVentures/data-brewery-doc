.. _specification:

Data Warehouse specification
============================


.. toctree::
   :maxdepth: 1

   specs/datastores
   specs/modules
   specs/processes


All parts of your data warehouse are making up the data warehouse specification. 
Here is a complete data warehouse specification example with explanation for each parts as HTML comments.


.. code-block:: xml

    <datawarehouse>
        <!-- Define where the data warehouse is located. -->
        <datastore name="dw" type="postgresql" host="dw.theowner.com" database="datawarehouse" 
            user="john" password="Doe" sshUser="john">
        </datastore>
        <!-- Define where the ERP (prestashop) is located. -->
        <datastore name="prestashop" type="mysql" host="theowner.com" database="prestashop" 
            user="john" password="Doe" sshUser="john">
            <!-- With autodiscover, DataFactory will analyse the prestashop schema of the database to get tables.  -->
            <autodiscovery schema="prestashop"/>
        </datastore>
        <!-- The Google Analytics data store.  -->
        <!-- Don't forget to allow DataFactory to access to your Google Analytics view.  -->
        <datastore name="ga" type="googleAnalytics" viewId="123456789">
            <table name="sessions" startDate="365daysago" endDate="yesterday">
                <!-- For the googleAnalytics datastore, notice that we use gaName and gaType.
                    gaName is the reference for the column in Google Analytics.
                    gaType is dimension or measure depending on the Google type.
                -->
                <column name="date" type="varchar" gaName="ga:date" gaType="dimension"/>
                <column name="medium" type="varchar" gaName="ga:medium" gaType="dimension"/>
                <column name="source" type="varchar" gaName="ga:source" gaType="dimension"/>
                <column name="campaign" type="varchar" gaName="ga:campaign" gaType="dimension"/>
                <column name="country" type="varchar" gaName="ga:country" gaType="dimension"/>
                <column name="city" type="varchar" gaName="ga:city" gaType="dimension"/>
                <column name="sessions" type="bigint" gaName="ga:sessions" gaType="measure"/>
                <column name="bounces" type="bigint" gaName="ga:bounces" gaType="measure"/>
                <column name="pageviews" type="bigint" gaName="ga:pageviews" gaType="measure"/>
            </table>
        </datastore>
        <!-- Integration layer module : prestashop  -->
        <!-- In the database it will be store under the m901_prestashop schema  -->
        <module id="901" name="prestashop" datastore="dw">
            <!-- We replicate all the prestashop datastore-->
            <replicate datastore="prestashop"/>
        </module>
        <!-- Integration layer module : Google Analytics  -->
        <!-- In the database it will be store under the m902_google_analytics schema  -->
        <module id="902" name="google_analytics" datastore="dw">
            <!-- We replicate all the ga (Google Analytics) datastore-->
            <replicate datastore="ga"/>
        </module>
        <!-- Refine/Presentation layer module : Queries for business analysts  -->
        <!-- In the database it will be store under the m401_reporting schema  -->
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

