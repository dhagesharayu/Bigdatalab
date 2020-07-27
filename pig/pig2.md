# Lab: Exploring Data with Pig
* About this Lab
* Objective: Use Pig to navigate through HDFS and explore a dataset.
* File locations: whitehouse/visits.txt in HDFS
* Successful outcome: You will have written several Pig scripts that analyze and query the White House visitors’ data, including a list of people who visited the President.

* Before you begin: At a minimum, complete steps 1 and 2 of the Getting Started with Pig1 lab.

Lab Steps
1. Load the White House Visitor Data

* You will use the TextLoader to load the visits.txt file. From the Pig Grunt shell,define the following LOAD relation:
```
pig
grunt> A = LOAD 'whitehouse/' USING TextLoader();
```

![image](https://user-images.githubusercontent.com/63589909/88380798-da6ac000-cdc2-11ea-8dc7-9a1ba8ffd07b.png)

2. Count the Number of Lines

* Define a new relation named B that is a group of all the records in A: ```grunt> B = GROUP A ALL;```

* Use DESCRIBE to view the schema of B. ```grunt> DESCRIBE B;```

![image](https://user-images.githubusercontent.com/63589909/88380888-00906000-cdc3-11ea-99d6-da1f67580913.png)

* What is the datatype of the group field? BAG
* Where did this datatype come from? PIG itself load it as a single group, i.e Group of all records ao its basically a collection of tuple. Each tuple representing a record.
* Why does the A field of B contain no schema? Because we did not define it will loading.
* How many groups are in the relation B? 1

* The A field of the B tuple is a bag of all of the records in visits.txt. Use the COUNT function on this bag to determine how many lines of text are in visits.txt:
```grunt> A_count = FOREACH B GENERATE 'rowcount', COUNT(A);```

* Note: The ‘rowcount’ string in the FOREACH statement is simply to demonstrate that
you can have constant values in a GENERATE clause. It is certainly not necessary;
it just makes the output nicer to read.

![image](https://user-images.githubusercontent.com/63589909/88381058-4c430980-cdc3-11ea-99e7-771b4983ed65.png)

* Use DUMP on A_count to view the result. The output should look like:

![image](https://user-images.githubusercontent.com/63589909/88381287-c5daf780-cdc3-11ea-8a94-7a101de1bd6d.png)

* We can now conclude that there are 447,598 rows of text in visits.txt.

3. Analyze the Data’s Contents

* We now know how many records are in the data, but we still do not have a clear
picture of what the records look like. Let’s start by looking at the fields of each
record. Load the data using PigStorage(‘,’) instead of TextLoader():
```
grunt> visits = LOAD '/whitehouse/' USING
PigStorage(',');
```
This will split up the fields by comma.
* Use a FOREACH...GENERATE command to define a relation that is a projection of
the first 10 fields of the visits relation. ```grunt> firstten = FOREACH visits GENERATE $0..$9;```

![image](https://user-images.githubusercontent.com/63589909/88381579-4d286b00-cdc4-11ea-9992-8a44e7519f0b.png)

* Use LIMIT to display only 50 records then DUMP the result.
* The output should be 50 tuples that represent the first 10 fields of visits:
```
grunt> firstten_limit = LIMIT firstten 50;
grunt> DUMP firstten_limit;
```
![image](https://user-images.githubusercontent.com/63589909/88381654-67624900-cdc4-11ea-9348-28bc59ad525e.png)

![image](https://user-images.githubusercontent.com/63589909/88381718-8cef5280-cdc4-11ea-9be6-5e6346cc68bb.png)

* Notice from the output that the first three fields are the person’s name. The next
seven fields are a unique ID, badge number, access type, time of arrival, post of
arrival, time of departure, and post of departure.

4. Locate the POTUS (President of the United States of America)

* There are 26 fields in each record, and one of them represents the visitee (the person being visited in the White House). 
* Your goal now is to locate this column and determine who has visited the President of the United States. 
* Define a relation that is a projection of the last seven fields ($19 to $25) of visits. 
* Use LIMIT to only output 500 records. 
```
grunt> lastfields = FOREACH visits GENERATE $19..$25;
grunt> lastfields_limit = LIMIT lastfields 500;
grunt> DUMP lastfields_limit;
```
* The output should look like:

![image](https://user-images.githubusercontent.com/63589909/88381901-fe2f0580-cdc4-11ea-9fec-6ea41ff3d5b7.png)

![image](https://user-images.githubusercontent.com/63589909/88381966-24ed3c00-cdc5-11ea-94fe-4a3497a3d234.png)

* It is not necessarily obvious from the output, but field $19 in the visits relation
represents the visitee. Even though you selected 500 records in the previous
step, you may or may not see POTUS in the output above. (The White House has
thousands of visitors each day, but only a few meet the President.)

* Use FILTER to define a relation that only contains records of visits where field $19 matches POTUS. 
* Limit the output to 500 records.
* The output should include only visitors who met with the President. For example:
```
grunt> potus = FILTER visits BY $19 MATCHES 'POTUS';
grunt> potus_limit = LIMIT potus 500;
grunt> DUMP potus_limit;
```

![image](https://user-images.githubusercontent.com/63589909/88382110-6bdb3180-cdc5-11ea-9c52-735a7921d48d.png)

![image](https://user-images.githubusercontent.com/63589909/88382715-a7c2c680-cdc6-11ea-959c-472c7af0ef26.png)

5. Count the POTUS Visitors
* Let’s discover how many people have visited the President. 
* To do this, we need to count the number of records in visits where field $19 matches POTUS.(use the one created earlier) 
* Use the potus relation from the previous step as a starting point. 
* You will need to use GROUP ALL and then a FOREACH projection that uses the COUNT function.
```
grunt> potus = FILTER visits BY $19 MATCHES 'POTUS';
grunt> potus_group = GROUP potus ALL;
grunt> potus_count = FOREACH potus_group GENERATE COUNT(potus);
grunt> DUMP potus_count;
```
![image](https://user-images.githubusercontent.com/63589909/88382802-d8a2fb80-cdc6-11ea-9a74-8630978e20c3.png)

![image](https://user-images.githubusercontent.com/63589909/88384011-3afcfb80-cdc9-11ea-8200-759a011d5560.png)

6. Finding People Who Visited the President

* So far you have used DUMP to view the results of your Pig scripts. In this step, you will save the output to a file using the STORE command.
* Now FILTER the relation by visitors who met with the President:```grunt> potus = FILTER visits BY $19 MATCHES 'POTUS';```

* Define a projection of the potus relationship that contains the name and time
of arrival of the visitor:
```
grunt> potus_details = FOREACH potus GENERATE
(chararray) $0 AS lname:chararray,
(chararray) $1 AS fname:chararray,
(chararray) $6 AS arrival_time:chararray,
(chararray) $19 AS visitee:chararray;
```

![image](https://user-images.githubusercontent.com/63589909/88383816-db065500-cdc8-11ea-8f3b-a83ae4cb823c.png)

* Order the potus_details projection by last name:```grunt> potus_details_ordered = ORDER potus_details BY lname ASC;```

* Store the records of potus_details_ordered into a folder named potus and using a comma delimiter:
```grunt> STORE potus_details_ordered INTO 'potus' USING PigStorage(',');```

![image](https://user-images.githubusercontent.com/63589909/88383713-a397a880-cdc8-11ea-8cea-610a2d2e4e6b.png)

f. View the contents of the potus folder:```grunt> ls potus```

![image](https://user-images.githubusercontent.com/63589909/88383457-2cfaab00-cdc8-11ea-8f48-f3a0853bbbf5.png)

* Notice that there is a single output file, so the Pig job was executed with one
reducer. View the contents of the output file using cat:```grunt> cat potus/part-```

![image](https://user-images.githubusercontent.com/63589909/88383498-426fd500-cdc8-11ea-8517-53ce12270894.png)

* The output should be in a comma‐delimited format and should contain the last
name, first name, time of arrival (if available), and the string POTUS:

* Result
You have written several Pig scripts to analyze and query the data in the White House visitors’
log. You should now be comfortable with writing Pig scripts with the Grunt shell and using
common Pig commands like LOAD, GROUP, FOREACH, FILTER, LIMIT, DUMP, and STORE.
