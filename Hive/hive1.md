# Lab: Understanding Hive Tables

* About this Lab
* Objective: Understand how Hive table data is stored in HDFS.
* File locations: labs/Lab7.1
* Successful outcome: A new Hive table filled with the data from the wh_visits folder.
Before you begin: Complete the Preparing Data for Hive lab, or put the data from the solution of that lab into HDFS.

## Lab steps

1. Review the Data

* Use the hdfs dfs -ls command to view the contents of the /apps/hive/warehouse/wh_visits/ folder in HDFS that was created in an
[earlier lab](https://github.com/dhagesharayu/Bigdatalab/blob/master/pig4.md).

On ```hdfs dfs -ls /apps/hive/warehouse/wh_visits/``` You should see part-m files:

![image](https://user-images.githubusercontent.com/63589909/88486772-b9aa9200-cf9d-11ea-8671-fa506ad10b44.png)

* the Pig projection to create these files had the following schema
```
project_potus = FOREACH potus GENERATE
$0 AS lname:chararray,
$1 AS fname:chararray,
$6 AS time_of_arrival:chararray,
$11 AS appt_scheduled_time:chararray,
$21 AS location:chararray,
$25 AS comment:chararray ;
```

* Define a Hive table that matches these records and contains the exported data from your Pig script.

2. Define a Hive Script

* In the Understanding labs/Lab7.1 folder, there is a text file named wh_visits.hive. View its contents.

![image](https://user-images.githubusercontent.com/63589909/88486863-5836f300-cf9e-11ea-90a4-ace69508c411.png)

* Notice that it defines a Hive table named wh_visits with the following schema that
matches the data in your project_potus folder.

* Note: You cannot use comment or location as column names because those are reserved
Hive keywords, so we changed them slightly.

* Run the script with the following command:```hive -f wh_visits.hive```

![image](https://user-images.githubusercontent.com/63589909/88486962-15294f80-cf9f-11ea-882b-18ac3a9dbf8e.png)

3. Verify the Table Creation

* Start the Hive Shell: ```hive```

* From the hive> prompt, enter the “show tables” command:
* You should see wh_visits in the list of tables.

* Use the describe command to view the details of wh_visits:

* ALso load the data ino it.

![image](https://user-images.githubusercontent.com/63589909/88487027-7c470400-cf9f-11ea-94e9-1536ec8f5fd1.png)

* Now your apps/hive/warehouse/wh_visits/ data will no longer be in your this folder as it has been loaded.

* Why is it gone? 
* The LOAD command does not copy files; it moves them.

* Try running a query

![image](https://user-images.githubusercontent.com/63589909/88487087-d47e0600-cf9f-11ea-903f-52c42530476e.png)

* Why did the previous query not require a Tez or MapReduce job to execute?
* Answer: The query selected all columns and did not contain a WHERE clause, the query
just needs to read in the data from the file and display it.

4. Count the Number of Rows in a Table

* Enter the following Hive query, which outputs the number of rows in wh_visits:

![image](https://user-images.githubusercontent.com/63589909/88487206-a77e2300-cfa0-11ea-9ac1-8c6e1e445a45.png)

* How many rows are currently in wh_visits: 21,819.

5. Selecting the Input File Name

* Hive has two virtual columns that get created automatically for every table:
INPUT__FILE__NAME and BLOCK__OFFSET__INSIDE__FILE.
* Note that between each word in the column name there are two underscore
characters, not just one.

* You can use these column names in your queries just like any other column of
the table.

![image](https://user-images.githubusercontent.com/63589909/88487271-283d1f00-cfa1-11ea-934d-5b7a2dc0723f.png)

* The result of this query is visitors to the White House whose last name starts
with “S.” Notice that the output also contains the particular file that the record
was found in.

6. Drop a Table

* Let’s see what happens when a managed/internal table is dropped.

* Define a simple table

* Use the Hive dfs command to put Lab7.1/names.txt into the HDFS

![image](https://user-images.githubusercontent.com/63589909/88487364-055f3a80-cfa2-11ea-9561-705ab98e00ae.png)

![image](https://user-images.githubusercontent.com/63589909/88487373-1740dd80-cfa2-11ea-84f6-e828b663c425.png)

* From the Hive Shell, run the following query:

![image](https://user-images.githubusercontent.com/63589909/88487395-49523f80-cfa2-11ea-9cbf-d40ea815d94b.png)

* Now drop the names table:

* View the contents of the table’s folder again. Notice the name folder is gone:

![image](https://user-images.githubusercontent.com/63589909/88487443-c41b5a80-cfa2-11ea-8409-c6396a9a8a12.png)

* Be careful when you drop a managed table in Hive. Make sure you either have the data backed up somewhere else or that you no longer want the data.

7. Define an External Table

* In this step you will see how external tables work in Hive. Start by putting
names.txt into HDFS.

* Create a folder in HDFS for the external table to store its data.

* Define the names table as external this time along with the Location.

* Verify that the table has data.

*  Drop the names table.

* View the contents of /user/hadoop/hivedemo. Notice that names.txt is still there.

![image](https://user-images.githubusercontent.com/63589909/88487529-7d7a3000-cfa3-11ea-94d1-2030362c76ae.png)

* Result
You have verified that the data for external tables is not deleted when the corresponding table
is dropped. Aside from this behavior, managed tables and external tables in Hive are essentially
the same. You now have a table in Hive named wh_visits that was loaded from the result of a
Pig job. You also have an external table called names that stores its data in
/user/hadoop/hivedemo. At this point, you should have a pretty good understanding of how Hive
tables are created and populated.




