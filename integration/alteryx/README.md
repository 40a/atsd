# Alteryx Designer


## Overview

Alteryx Designer is a graphical design environment to create and edit ETL
(Extract, Transform, Load) workflows. The following guide includes examples of
loading time series data from the Axibase Time Series Database (ATSD),
calculating derived time series in Alteryx and storing the results back in ATSD.

## Sample Dataset

For these instructions we will use [sample series commands](resources/commands.txt). The series contain the Consumer Price Index (CPI) for each category
of items in a consumer's basket as well as a weight for each category in the CPI
basket. The weights are stored as fractions of 1000. The CPI is tracked from 2013 to
2017 and uses Year 2016 values as the baseline. Weight values are available only for year 2017.

To load the data into ATSD, login into the database web interface and submit these commands on the 
**Metrics→Data Entry** page.

![](images/metrics_entry.png)

## Prerequisites

- Install Alteryx Designer
- Install [ODBJ-JDBC gateway](../odbc/README.md).

## Create Database Connection

- Add the **Input Data** tool to your workflow.

  ![](images/input_data.png)

- Choose **Other Databases > ODBC...** in the Input Data configuration window.
- Open **ODBC Connection** dialog.

  ![](images/choose_odbc.png)

  ![](images/no_dsn.png)

- Open the **ODBC Data Source Administrator** window by pressing the **ODBC Admin** button in the **ODBC Connection** dialog. 

- Configure the datasource as described [here](../odbc/README.md#configure-odbc-data-source).

- Add `tables` property into the DSN URL to filter metrics by name in the Query Builder. For example, `tables=*` exposes all metrics whereas `tables=infla*` shows all metrics that start with 'infla' substring.

  ```text
  jdbc:axibase:atsd:ATSD_HOST:8443;tables=*
  ```
  
> Read more about the `table` property in the [JDBC driver](https://github.com/axibase/atsd-jdbc#jdbc-connection-properties-supported-by-driver) documentation.

- Unckeck the **Strip Quote** flag and press **OK**.

  ![](images/odbc_quotes.png)

- Select the name you specified for DSN during bridge configuration from the **Data
  Source Name** list and press **OK**.

  ![](images/dsn_list.png)

## Building Queries

After creating a connection you will see the **Choose Table or Specify Query** dialog.

![](images/choose_table.png)

It allows you build the query by choosing a table with columns or enterting query text manually.

- Table names visible in the **Tables** tab satisfy the `tables` pattern specified in the DSN URL. Click the **Refresh** button to reload the list, if neccessary.

  ![](images/metrics_list.png)

- In the **Visual Query Tab**, you can specify particular columns and add optional
  sorting and grouping to preview and prepare your
  data before processing it in workflow. Below is a SQL query and a corresponding configuration.

  ```sql
   Select
      inflation.cpi.categories.price.datetime,
      Sum(inflation.cpi.categories.price.value) As Sum_value
   From inflation.cpi.categories.price
   Group By inflation.cpi.categories.price.datetime
   Having Sum(inflation.cpi.categories.price.value) > 1010
   Order By inflation.cpi.categories.price.datetime
  ```

  ![](images/visual_builder.png)

- The **SQL Editor** allows you to review and modify pre-built queries or write
  your own queries. 
  
- The **Test Query** button strips the `WHERE` clause from the query and sends only the remaining `SELECT` expression to the database for validation.

  ![](images/sql_editor.png)

Press **OK** when the query is ready for processing.

### Check Query Results

Press **Run Workflow**, to see the result of the query.

![](images/run_workflow.png)

![](images/results.png)

## Calculate and Store Derived Series

This section demonstrates how to create an Alteryx Designer workflow that uses existing data in ATSD to produce new series and then store these series back in ATSD.
To calculate a weighted inflation index we multiply the CPI of each category by
its weight divided by 1000 and sum the products.

The resulting workflow will be implemented as follows:

![](images/workflow.png)

The workflow consists of the following steps (nodes):

1. **Input Data** tool.
   Repeat the steps in the previous section for this tool, choose
   `inflation.cpi.categories.price` table. Select `datetime`,
   `value` columns and manually add `tags.category` as shown below.

   ![](images/select_columns.png)

2. **Input Data** tool. Follow the same procedure as above for the
   `inflation.cpi.categories.weight` table.

3. **Filter** tool. Specify the condition `>= January 1st, 2010`
   and use the **T** (_true_) node output to retrieve the series created after 2009 only.

   ![](images/filter.png)

   ![](images/true_output.png)

4. **Filter** tool. Follow the same procedure as above.

5. **Join** tool. Join prices and weights by `tags.category` field. Deselect fields as shown
   on the image. Rename `value` fields for `inflation.cpi.categories.price` and
   `inflation.cpi.categories.weight` to `price` and `weight` respectively.

   ![](images/join.png)

   > **Note**
   >
   > To check input or output of any node, _run the workflow_ and click its
   > input/output.
   >
   > ![](images/join_output.png)

6. **Formula** tool. Connect its input to the **J** (_inner join_)
   output of the 3rd node. Next, create a new column named `value` to store the result.
   Fill in the expression to calculate it, and specify the correct data type.

   ![](images/add_column.png)

   ![](images/formula.png)

7. **Summarize** tool. Select fields from above to get the actions list as shown
   below on the image. Input `value` into  **Output Field Name**.

   ![](images/summarize.png)

8. **Sort** tool. Apply it to sort records by date.

   ![](images/sort.png)

9. **Formula** tool. Add an `entity` column with the **Formula** tool. Name it
   `bls.gov`. The default data type `V_WString` is not supported yet by the ATSD JDBC driver,
   use `String` or `WString` (for Unicode) instead.

   ![](images/entity.png)

10. **Output Data** tool. Choose ODBC Connection as before and enter a name for
    the _existing_ metric, in this case `inflation.cpi.composite.price`. Create a new metric in ATSD if necessary.
    Edit **Output Options** and **Table/FieldName SQL Style** options in the
    configuration dialog.

    ![](images/metric_name.png)

    ![](images/output.png)

11. **Browse** tool. View the final result.

Press **Run Workflow**.

The data will be retrieved from the database, processed in Designer by the workflow with new series stored back in the database . Click **Browse** node.

   ![](images/calc_results.png)
