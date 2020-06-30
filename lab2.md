# Lab: Importing RDMS Data into HDFS
## Steps
1.	[Steps to create and RDS and connect]( https://github.com/dhagesharayu/Cloud_Computing/blob/Services/RDS.md)

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

4. Import the Table into HDFS
* Enter the following Sqoop command (all on a single line), which imports the salaries table in the test database into HDFS:

``` sqoop import --connect jdbc:mysql://Endpoint-of-database/databasename --username  <> --password <> -- table salaries ```

* A MapReduce job should start executing, and it may take a couple of minutes for the job to complete.

![image](https://user-images.githubusercontent.com/63589909/86051779-94e0fe80-ba73-11ea-9eaf-97312c88ff9b.png)

![image](https://user-images.githubusercontent.com/63589909/86051792-9a3e4900-ba73-11ea-91d2-efd2d12cf911.png)

5. Verify the Import
* View the contents of your HDFS folder: ```hdfs dfs -ls```

* You should see a new folder named salaries. View its contents: ```hdfs dfs -ls salaries```

![image](https://user-images.githubusercontent.com/63589909/86051801-a0342a00-ba73-11ea-8fc3-89a2e7d5796b.png)

* Notice there are four new files in the salaries folder named part‐m‐0000x. Why are there four of these files?
* Answer: The MapReduce job that executed the Sqoop command used four mappers, so there are four output files (one from each mapper).
* Use the cat command to view the contents of the files. For example: ```hdfs dfs -cat salaries/part-m-00000```

![image](https://user-images.githubusercontent.com/63589909/86051811-a4604780-ba73-11ea-8943-a8f1d5190956.png)

* Notice the contents of these files are the rows from the salaries table in MySQL. You have now successfully imported data from a MySQL database into HDFS. Also notice that you imported the entire table with all of its columns. Next, you will
import only specific columns of a table.

![image](https://user-images.githubusercontent.com/63589909/86051824-a9bd9200-ba73-11ea-9e2e-d8f315088801.png)

6 Specify Columns to Import

* Using the ‐‐columns argument, write a Sqoop command that imports the salary and age columns (in that order) of the salaries table into a directory in HDFS
named salaries2. In addition, set the ‐m argument to 1 so that the result is a single file.
* Solution: The command you enter in the command line will look like this in the terminal window:

``` 
sqoop import --connect jdbc:mysql://sandbox/test?user=root Endpoint-of-database/databasename --username  <> --password<> --table salaries
--columns salary,age -m 1 --target-dir salaries2
```

![image](https://user-images.githubusercontent.com/63589909/86051844-b4782700-ba73-11ea-83e1-d251840cf03c.png)

![image](https://user-images.githubusercontent.com/63589909/86051851-b80bae00-ba73-11ea-9c4a-38de1314d5b8.png)

* To make it easier to read, following is the same command as above, however we have broken it down into smaller chunks separated by a "\" at the end of the break point in each line. When you see this formatting in the lab, you should type it out as it appears above, and do not enter the \ characters unless specifically instructed to do so.

* After the import, verify you only have one part‐m file in salaries2.

![image](https://user-images.githubusercontent.com/63589909/86051866-bc37cb80-ba73-11ea-87b2-8067fd641535.png)

* Verify that the contents of part‐m‐00000 are only the two columns you specified:

![image](https://user-images.githubusercontent.com/63589909/86051939-dd002100-ba73-11ea-9a0f-949b30f88f73.png)

![image](https://user-images.githubusercontent.com/63589909/86052071-05881b00-ba74-11ea-895b-658585d59272.png)

![image](https://user-images.githubusercontent.com/63589909/86128252-c6eb7280-bafe-11ea-95c2-51bb8670fed2.png)

![image](https://user-images.githubusercontent.com/63589909/86128306-d8cd1580-bafe-11ea-8ec8-299a4a1ba174.png)

![image](https://user-images.githubusercontent.com/63589909/86128343-e1255080-bafe-11ea-9d55-42d253d06904.png)

![image](https://user-images.githubusercontent.com/63589909/86128672-4f6a1300-baff-11ea-8112-a8f98e39546e.png)

![image](https://user-images.githubusercontent.com/63589909/86128687-55f88a80-baff-11ea-9774-af802065723f.png)

![image](https://user-images.githubusercontent.com/63589909/86128709-5bee6b80-baff-11ea-989e-f2248bd6f230.png)

![image](https://user-images.githubusercontent.com/63589909/86128830-84766580-baff-11ea-95cc-0939d6b0008c.png)

![image](https://user-images.githubusercontent.com/63589909/86128870-8b9d7380-baff-11ea-88fb-5bc7076ee0ce.png)

![image](https://user-images.githubusercontent.com/63589909/86128899-92c48180-baff-11ea-9ed0-9aa182d479ba.png)

![image](https://user-images.githubusercontent.com/63589909/86128935-99eb8f80-baff-11ea-870e-07f28c565946.png)

![image](https://user-images.githubusercontent.com/63589909/86128955-9e17ad00-baff-11ea-90d9-3fca58964ac6.png)

![image](https://user-images.githubusercontent.com/63589909/86128968-a243ca80-baff-11ea-8cc4-40238282e21b.png)

![image](https://user-images.githubusercontent.com/63589909/86128378-ec787c00-bafe-11ea-8b51-464e0259b797.png)

![image](https://user-images.githubusercontent.com/63589909/86128406-f306f380-bafe-11ea-9807-d7d3f429edc0.png)

![image](https://user-images.githubusercontent.com/63589909/86129014-b2f44080-baff-11ea-812e-61880777a562.png)