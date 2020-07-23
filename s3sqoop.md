# Cloud Storage Layer (AWS s3) as HDFS
* The hadoop and cloud storage vendors worked together in such a way that Cloud Storage Layer cna be treated as HDFS.
## Lab 2.2: Using Sqoop commands  
## Importing RDMS Data into S3(HDFS)
<details>
<summary>Steps</summary>
<br>

1.[Steps to create and RDS and connect]( https://github.com/dhagesharayu/Cloud_Computing/blob/Services/RDS.md)

2.	Create table and import data in RDMS it as shown.

![image](https://user-images.githubusercontent.com/63589909/86019452-3fd9c400-ba44-11ea-8019-43427f584f14.png)

![image](https://user-images.githubusercontent.com/63589909/86019520-554eee00-ba44-11ea-81b3-9f4f6984b193.png)

![image](https://user-images.githubusercontent.com/63589909/86051368-eb017200-ba72-11ea-9208-55b03ea90e50.png)

![image](https://user-images.githubusercontent.com/63589909/86020662-d22e9780-ba45-11ea-9e1e-65e257491552.png)

![image](https://user-images.githubusercontent.com/63589909/86051169-9eb63200-ba72-11ea-8eeb-b6e8d88ef2f6.png)

![image](https://user-images.githubusercontent.com/63589909/86051273-c7d6c280-ba72-11ea-9980-a9d2c5df7b87.png)

![image](https://user-images.githubusercontent.com/63589909/86051326-dc1abf80-ba72-11ea-876b-329824e3f3b4.png)

![image](https://user-images.githubusercontent.com/63589909/86051633-59463480-ba73-11ea-856b-f0cb55949371.png)

3. Steps to create hadoop cluster with sqoop: 

![image](https://user-images.githubusercontent.com/63589909/86051461-11bfa880-ba73-11ea-9c4d-34d59497c3f6.png)

![image](https://user-images.githubusercontent.com/63589909/86051475-16845c80-ba73-11ea-992c-1dbb70fd2331.png)

![image](https://user-images.githubusercontent.com/63589909/86051486-1be1a700-ba73-11ea-8d40-aa9289c94a37.png)

![image](https://user-images.githubusercontent.com/63589909/86051495-20a65b00-ba73-11ea-86ef-5bcde6ec8215.png)

![image](https://user-images.githubusercontent.com/63589909/86051509-269c3c00-ba73-11ea-8e96-81de2312e56f.png)

![image](https://user-images.githubusercontent.com/63589909/86051516-2ac85980-ba73-11ea-94c6-61b39c18ec69.png)

* [Steps for SSH to EMR](https://github.com/dhagesharayu/Bigdatalab/blob/master/EMRSetup%20with%20Hadoop.md)

4. [Steps to create s3 bucket](https://github.com/dhagesharayu/Cloud_Computing/blob/Services/S3.md)

* Make sure your s3 Bucket and RDS Database is publicly accessible.

5. Import the Table into s3
* Enter the following Sqoop command (all on a single line), which imports the salaries table in the test database into s3:

``` sqoop import --connect jdbc:mysql://Endpoint-of-database/databasename --username  <> --password <> -- table salaries --target-dir s3a://bucketname/salaries```

* A MapReduce job should start executing, and it may take a couple of minutes for the job to complete.

![image](https://user-images.githubusercontent.com/63589909/88274532-7f20cb00-ccf9-11ea-8c34-180485ff5068.png)

![image](https://user-images.githubusercontent.com/63589909/88274628-a11a4d80-ccf9-11ea-8571-5406cda13c93.png)

6. Verify the Import
* View the contents of your HDFS folder: ```hdfs dfs -ls s3a://bucketname/path```

* You should see a new folder named salaries. View its contents: ```hdfs dfs -ls s3a://bucketname/salaries```

![image](https://user-images.githubusercontent.com/63589909/88274883-1423c400-ccfa-11ea-8b03-d0a2da9ceb9e.png)

* Notice there are four new files in the salaries folder named part‐m‐0000x. Why are there four of these files?

* Answer: The MapReduce job that executed the Sqoop command used four mappers, so there are four output files (one from each mapper).

* Use the cat command to view the contents of the files. For example: ```hdfs dfs -cat s3a://bucketname/salaries/part-m-00000```


![image](https://user-images.githubusercontent.com/63589909/88275017-4af9da00-ccfa-11ea-8711-2a3f2729e0b7.png)

* Notice the contents of these files are the rows from the salaries table in MySQL. You have now successfully imported data from a MySQL database into s3. Also notice that you imported the entire table with all of its columns. Next, you will
import only specific columns of a table.


7. Specify Columns to Import

* Using the ‐‐columns argument, write a Sqoop command that imports the salary and age columns (in that order) of the salaries table into a directory in s3. 
* In addition, set the ‐m argument to 1 so that the result is a single file.

``` 
sqoop import --connect jdbc:mysql://Endpoint-of-database/databasename --username  <> --password<> --table salaries
--columns salary,age -m 1 --target-dir s3a://bucketname/directory_name
```

![image](https://user-images.githubusercontent.com/63589909/88275840-abd5e200-ccfb-11ea-8ae2-aa29d7e7475f.png)

![image](https://user-images.githubusercontent.com/63589909/88275940-d3c54580-ccfb-11ea-9fab-fbf684896952.png)

* To make it easier to read, the command can be broken down into smaller chunks separated by a "\" at the end of the break point in each line.

* After the import, verify you only have one part‐m file.

![image](https://user-images.githubusercontent.com/63589909/88276141-31f22880-ccfc-11ea-8cd1-4929ed927943.png)

* Verify that the contents of part‐m‐00000 are only the two columns you specified:

![image](https://user-images.githubusercontent.com/63589909/88276190-41717180-ccfc-11ea-91f0-e2f40021f0b4.png)

![image](https://user-images.githubusercontent.com/63589909/88286316-2dcf0680-cd0e-11ea-9dbd-f3b5f26661ee.png)

![image](https://user-images.githubusercontent.com/63589909/88286422-59ea8780-cd0e-11ea-892d-ecc8c1dd01ac.png)

![image](https://user-images.githubusercontent.com/63589909/88286519-81415480-cd0e-11ea-8beb-f67caa61dbf5.png)

7. Importing from a Query

* Write a Sqoop import command that imports the rows from salaries in MySQL whose salary column is greater than 90,000.00.
* Use gender as the --split-by value, specify only two mappers, and import the data into s3 Bucket.
* The Sqoop command looks similar to the ones you have been using throughout this lab, except you will use --query instead of --table. Recall that when you use
a --query command you must also define a --split-by column, or define -m to be 1. Also, do not forget to add $CONDITIONS to the WHERE clause of your query, as
demonstrated earlier in this unit.

![image](https://user-images.githubusercontent.com/63589909/88276783-3ff47900-ccfd-11ea-8b88-be1432f5506c.png)

![image](https://user-images.githubusercontent.com/63589909/88277893-189eab80-ccff-11ea-8241-a3b1dcadafd1.png)

* To verify the result, view the contents of the files in salaries7. You should have only two output files.
```hdfs dfs -ls s3a://bucketname/directory_name```

![image](https://user-images.githubusercontent.com/63589909/88278088-759a6180-ccff-11ea-81b7-343913a6c89b.png)

* View the contents of part‐m‐00000 and part‐m‐00001.
```
hdfs dfs -cat s3a://bucketname/directory_name/part-m-00000
hdfs dfs -cat s3a://bucketname/directory_name/part-m-00001
```
![image](https://user-images.githubusercontent.com/63589909/88278154-91056c80-ccff-11ea-9b7c-b6e3cd51eb13.png)

* Notice that one file contains females, and the other file contains males. Because we used gender as the split‐by column, so all records with the same
gender are sent to the same mapper and the output files contain only records whose salary is greater than 90,000.00.

* sqoop eval

![image](https://user-images.githubusercontent.com/63589909/88286645-b188f300-cd0e-11ea-8ac9-bcfad8e86b43.png)

![image](https://user-images.githubusercontent.com/63589909/88286739-d8472980-cd0e-11ea-8673-4c95fe7c9651.png)

</details>

## Exporting S3(HDFS) Data to an RDBMS
<details>
<summary>Steps</summary>
<br>
  
1. [Steps to create and RDS and connect]( https://github.com/dhagesharayu/Cloud_Computing/blob/Services/RDS.md)

2. Create the table salaries2 in database as shown

![image](https://user-images.githubusercontent.com/63589909/86128830-84766580-baff-11ea-95cc-0939d6b0008c.png)

* Steps to create hadoop cluster with scoop and perform SSH shown in Importing RDMS Data into HDFS Steps.

3. [Steps to create s3 bucket](https://github.com/dhagesharayu/Cloud_Computing/blob/Services/S3.md)

* Make sure your s3 Bucket and RDS Database is publicly accessible.

3. The Data in the s3(hdfs) to be exported listed below. 

![image](https://user-images.githubusercontent.com/63589909/88279043-0887cb80-cd01-11ea-8199-79688324a299.png)

![image](https://user-images.githubusercontent.com/63589909/88279095-248b6d00-cd01-11ea-8412-d75169a5c098.png)

4. Export the Data
* Run a Sqoop command that exports the salarydata folder in HDFS into the salaries2 table in MySQL. At the end of the MapReduce output, you should see a
log event stating that 10,000 records were exported.

![image](https://user-images.githubusercontent.com/63589909/88278826-aaf37f00-cd00-11ea-87bb-a40f9c39f30a.png)

![image](https://user-images.githubusercontent.com/63589909/88278870-bb0b5e80-cd00-11ea-8bbd-decf96fd3ef0.png)

5. Verify it worked by viewing the table’s contents from the mysql.

![image](https://user-images.githubusercontent.com/63589909/86129014-b2f44080-baff-11ea-812e-61880777a562.png)

</details>
