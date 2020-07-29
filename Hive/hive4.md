# Lab: Computing ngrams of Emails in Avro Format
* About this Lab
* Objective: Use Hive to compute ngrams.
* File locations: labs/Lab7.5
* Successful outcome: A bigram of words found in a collection of Avro‐formatted emails.

## Lab Steps

![image](https://user-images.githubusercontent.com/63589909/88830540-b99fd180-d1eb-11ea-8386-42385ae9f2b4.png)

![image](https://user-images.githubusercontent.com/63589909/88830577-c6242a00-d1eb-11ea-865d-0610e03de0d9.png)

1. View an Avro Schema

* Change directories to the labs/Lab7.5 folder. Notice this folder
contains an Avro file named sample.avro.

* Create a schema file for sample.avro:

![image](https://user-images.githubusercontent.com/63589909/88830711-f7045f00-d1eb-11ea-9147-430da3d938fa.png)

* Put the schema file in HDFS:

![image](https://user-images.githubusercontent.com/63589909/88830766-071c3e80-d1ec-11ea-98ee-c55f5514727a.png)

2. Create a Hive Table from an Avro Schema

* View the contents of the CREATE TABLE query defined in the
create_sample_table.hive file in your Computing ngrams of Emails in Avro
Format lab folder:

![image](https://user-images.githubusercontent.com/63589909/88830806-19967800-d1ec-11ea-83e8-4a5f16d49b4e.png)

* Make sure the avro.schema.url property points to the schema file you created in
the previous step:
```
WITH SERDEPROPERTIES (
'avro.schema.url'='hdfs:///user/hadoop/sample.avsc')
```

![image](https://user-images.githubusercontent.com/63589909/88830889-359a1980-d1ec-11ea-9175-17010ff9542a.png)

* Run the CREATE TABLE query:

![image](https://user-images.githubusercontent.com/63589909/88830960-4cd90700-d1ec-11ea-88f5-952f9f0977d5.png)

3. Verify the Table

* Start the Hive shell.```hive```

* Run the show tables command and verify that you have a table named

![image](https://user-images.githubusercontent.com/63589909/88831010-5bbfb980-d1ec-11ea-8dcc-d84e7c4bbfec.png)


* Run the describe command on sample_table.

![image](https://user-images.githubusercontent.com/63589909/88831025-60846d80-d1ec-11ea-9f9d-51836e84b7f1.png)

![image](https://user-images.githubusercontent.com/63589909/88831188-9e819180-d1ec-11ea-88b6-de98b04140c9.png)

* Let’s associate some data with sample_table. 
* Copy sample.avro into the table location

* View the contents of sample_table, then quit the Hive Shell:

![image](https://user-images.githubusercontent.com/63589909/88831287-c244d780-d1ec-11ea-88e9-c0e8c8d249ea.png)

* Note that there is only one record in sample.avro. You have now seen how to create a Hive
table using an Avro schema file. This was a simple example; next you will complete these
steps using a large data file that contains emails in an Avro format.

4. Create an Email‐User Table

* There is an Avro file in your labs/Lab7.5 folder named mbox7.avro,
which represents emails in an Avro format from a Hive mailing list for the month
of July. 

* Run the getschema command to get the schema in a file named mbox.avsc:


* Put the Avro schema file into in HDFS:

* Use more to view the contents of the create_email_table.hive ;:script in your labs/Lab7.5 folder. 


* Verify the avro.schema.url property is correct.



* Run the script to create the hive_user_email table:



* Copy mbox7.avro into the table location



* Start the Hive shell and verify the table has data in it:


5. Compute a Bigram

* Use the Hive ngrams function to create a bigram of the words in mbox7.avro:



* The output will be kind of a jumbled mess:


* To clean this up, use the Hive explode function to display the output in a more
readable format:



* You should see a nice, readable list of 10 bigrams:


* Typically when working with word comparison we ignore case. 

* Run the query
again, but this time add the Hive lower function and compute 20 bigrams:


* The output should look like the following:

6. Compute a Context ngram

* From the Hive shell, run the following query, which uses the context_ngrams
function to find the top 20 terms that follow the word “error”:


* The output should look like the following:

* What is the most likely word to follow “error” in these emails?
* Answer: “in”

* Run a Hive query that finds the top 20 results for words in mbox7.avro that follow
the phrase “error in.”

* Result
You have written several Hive queries that computed bigrams based on the data in the
mbox7.avro file. You should also be familiar with working with Avro files, a popular file format in
Hadoop.
