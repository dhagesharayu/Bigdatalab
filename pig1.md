# Lab: Getting Started with Pig

* About this Lab
* Objective: Use Pig to navigate through HDFS and explore a dataset.
* File locations: labs/Lab5.1
* Successful outcome: You will have a couple of Pig programs that load the White House
visitors’ data, with and without a schema, and store the output of
a relation into a folder in HDFS.

Before you begin: Your HDP 2.3 cluster should be up and running within your VM.

## Lab Steps
1. View the Raw Data

* If not already done, open a Terminal in your VM and type "ssh sandbox".

* Change directories to the labs/Lab5.1 folder:

![image](https://user-images.githubusercontent.com/63589909/88369310-58bc6780-cdad-11ea-855a-93e282436dd7.png)

* Unzip the archive in the labs/Lab5.1 folder, which contains a file named whitehouse_visits.txt that is quite large:
```unzip whitehouse_visits.zip```

![image](https://user-images.githubusercontent.com/63589909/88369470-bd77c200-cdad-11ea-86ba-3ad496e0a941.png)

* View the contents of this file: ```tail whitehouse_visits.txt```

![image](https://user-images.githubusercontent.com/63589909/88369642-1ba4a500-cdae-11ea-9c20-bdf659cc7121.png)

* This publicly available data contains records of visitors to the White House in
Washington, D.C.

2. Load the Data into HDFS

* Start the Grunt shell: ```pig```

![image](https://user-images.githubusercontent.com/63589909/88369721-3ecf5480-cdae-11ea-89ae-a00bb01cd223.png)

* From the Grunt shell, make a new directory in HDFS named whitehouse:
```grunt> mkdir whitehouse```

![image](https://user-images.githubusercontent.com/63589909/88369837-7dfda580-cdae-11ea-8349-2519b412008c.png)

* Use the copyFromLocal command in the Grunt shell to copy the whitehouse_visits.txt file to the whitehouse folder in HDFS, renaming the
file visits.txt. 
```grunt> copyFromLocal whitehouse_visits.txt whitehouse/visits.txt```

![image](https://user-images.githubusercontent.com/63589909/88370290-6541bf80-cdaf-11ea-8639-d2b621fdfd8f.png)

* No need to mention path as you are already in that directory other mention it /user/hadoop/lab......

* Use the ls command to verify that the file was uploaded successfully:

![image](https://user-images.githubusercontent.com/63589909/88370340-7db1da00-cdaf-11ea-89a1-35dc32ef60ec.png)

3. Define a Relation
* You will use the TextLoader to load the visits.txt file.
* Note: TextLoader simply creates a tuple for each line of text, and it uses a single
chararray field that contains the entire line. It allows you to load lines of text and
not worry about the format or schema yet.
* Define the following LOAD relation: ```grunt> A = LOAD 'whitehouse/visits.txt' USING TextLoader();```

* Use DESCRIBE to notice that A does not have a schema:

![image](https://user-images.githubusercontent.com/63589909/88370740-455ecb80-cdb0-11ea-9d0c-6c0c5458a767.png)

* We want to get a sense of what this data looks like. Use the LIMIT operator to define a new relation named A_limit that is limited to 10 records of A.
* ```grunt> A_limit = LIMIT A 10;```

* Use the DUMP operator to view the A_limit relation.

* Each row in the output will look similar to the following and should be 10
arbitrary rows from visits.txt:

![image](https://user-images.githubusercontent.com/63589909/88370873-8e168480-cdb0-11ea-9295-4f107ecac74a.png)

![image](https://user-images.githubusercontent.com/63589909/88370891-9a024680-cdb0-11ea-8371-1a9416ce85e5.png)


4. Define a Schema
* Load the White House data again, but this time use the PigStorage loader and
also define a partial schema:
```
grunt> B = LOAD 'whitehouse/visits.txt' USING
PigStorage(',') AS (lname:chararray,fname:chararray,mname:chararray,id:chararray,status:chararray,state:chararray,arrival:chararray);
```

* Use the DESCRIBE command to view the schema: ```grunt> describe B;```

![image](https://user-images.githubusercontent.com/63589909/88371063-f36a7580-cdb0-11ea-9049-2d8c1f8a8781.png)

5. The STORE Command

* Enter the following STORE command, which stores the B relation into a folder
named whouse_tab and separates the fields of each record with tabs:```grunt> store B into 'whouse_tab' using PigStorage('\t');```

![image](https://user-images.githubusercontent.com/63589909/88371238-447a6980-cdb1-11ea-8270-043e2918492b.png)

* Verify that the whouse_tab folder was created:```grunt> ls whouse_tab```

![image](https://user-images.githubusercontent.com/63589909/88371339-755a9e80-cdb1-11ea-800d-835550e223f5.png)

* You should see two map output files.

* View one of the output files to verify they contain the B relation in a tab‐ delimited format:
```grunt> fs -tail whouse_tab/part-```

![image](https://user-images.githubusercontent.com/63589909/88371494-c36fa200-cdb1-11ea-8297-572aaf5ea02c.png)

* Each record should contain seven fields. What happened to the rest of the fields from the raw data that was loaded from whitehouse/visits.txt?

* Answer: They were simply ignored when each record was read in from HDFS.

6. Use a Different Storer

* In the previous step, you stored a relation using PigStorage with a tab delimiter.

* Enter the following command, which stores the same relation but in a JSON format: ```grunt> store B into 'whouse_json' using JsonStorage();```

![image](https://user-images.githubusercontent.com/63589909/88371605-fe71d580-cdb1-11ea-9283-fbbf4e93040f.png)

* Verify that the whouse_json folder was created:```grunt> ls whouse_json```

* View one of the output files:```grunt> fs -tail whouse_json/part-```

![image](https://user-images.githubusercontent.com/63589909/88371746-41cc4400-cdb2-11ea-9f36-5dfbd0352a03.png)

* Notice that the schema you defined for the B relation was used to create the
format of each JSON entry:

Result
You have now seen how to execute some basic Pig commands, load data into a relation, and
store a relation into a folder in HDFS using different formats.

* USE scripDone to exit pig: ![image](https://user-images.githubusercontent.com/63589909/88370113-0714dc80-cdaf-11ea-9dc2-e10207cd72ce.png)
