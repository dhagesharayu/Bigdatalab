pig 5
On a Web site, clickstream analysis (also called clickstream analytics) is the process of collecting, analyzing and reporting aggregate data about which pages a website 
visitor visits -- and in what order. 
The path the visitor takes though a website is called the clickstream.
# Lab: Splitting a Dataset
* About this Lab
* Objective: Research the White House visitor data and look for members of Congress.

* Successful outcome: Two folders in HDFS, congress and not_congress, containing a split of the White House visitor data.

* Before you begin: You should have the White House visitor data in HDFS in /user/hadoop/whitehouse/visits.txt.

## Lab Steps

1. Explore the Comments Field

* In this step, you will explore the comments field of the White House visitor data.

* From the Pig Grunt shell, start by loading visits.txt:
```pig
grunt> cd whitehouse
grunt> visits = LOAD 'visits.txt' USING PigStorage(',');
```

* Field $25 is the comments. Filter out all records where field $25 is null:
```
grunt> not_null_25 = FILTER visits BY ($25 IS NOT NULL);
```

* Now define a new relation that is a projection of only column $25:
```grunt> comments = FOREACH not_null_25 GENERATE $25 AS comment;```

* View the schema of comments and make sure you understand how this relation ended up as a tuple with one field:
```
grunt> describe comments;
comments: {comment: bytearray}
```

2. Test the Relation

* A common Pig task is to test a relation to make sure it is consistent with what you are intending it to be. 

* But using DUMP on a big data relation might take too long or not be practical, so define a SAMPLE of comments:
```grunt> comments_sample = SAMPLE comments 0.001;```

* Now DUMP the comments_sample relation. The output should be non‐null comments about visitors to the White House, similar to:
```grunt> DUMP comments_sample;```

3. Count the Number of Comments

* The comments relation represents all non‐null comments from visits.txt. 
* Write Pig statements that output the number of records in the comments relation. 
* The correct result is 222,839 records.
```
comments_all = GROUP comments ALL;
comments_count = FOREACH comments_all GENERATE
COUNT(comments);
DUMP comments_count;
```
4. Split the Dataset

* Note: Our end goal is find visitors to the White House who are also members of Congress. 
* We could run our MapReduce job on the entire visits.txt dataset.
* But it is common in Hadoop to split data into smaller input files for specific tasks, which can greatly improve the performance of your MapReduce applications. 
* In this step, you will split visits.txt into two separate datasets.

* In this step, you will split visits.txt into two datasets: those that contain “CONGRESS” in the comments field, and those that do not.

* Use the SPLIT command to split the visits relation into two new relations named congress and not_congress:
```
grunt> SPLIT visits INTO congress IF($25 MATCHES'.* CONGRESS .*'), not_congress IF (NOT($25 MATCHES'.* CONGRESS .*'));
```

* Store the congress relation into a folder named ‘congress’. ```grunt> STORE congress INTO 'congress';```

* Similarly, STORE the not_congress relation in a folder named ‘not_congress’. ```grunt> STORE not_congress INTO 'not_congress';```

* View the output folders using ls. The file sizes should be equivalent to the following:
```grunt> ls congress```

* View one of the output files in congress and make sure the string “CONGRESS” appears in the comment field:
```grunt> cat congress/part-m-00000```

5. Count the Results

* Write Pig statements that output the number of records in the congress relation.

* This will tell us how many visitors to the White House have “CONGRESS” in the
comments of their visit log. The correct result is 102.

* Note: You now have two datasets: one in ‘congress,’ with 102 records, and the
remaining records in the ‘not_congress’ folder. 
* These records are still in their
original, raw format.
```
grunt> congress_grp = GROUP congress ALL;
grunt> congress_count = FOREACH congress_grp GENERATE
COUNT(congress);
grunt> DUMP congress_count;
```

* Result
You have just split ‘visits.txt’ into two datasets, and you have also discovered that 102 visitors
to the White House had the word “CONGRESS” in their comments field. We will further explore
these visitors in the next lab as we perform a join with a dataset containing the names of
members of Congress.
