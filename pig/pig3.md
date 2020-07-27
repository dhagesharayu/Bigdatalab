# Lab: Joining Datasets with Pig
* About this Lab
* Objective: Join two datasets in Pig.
* File locations: labs/Lab6.2
* Successful outcome: A file of members of Congress who have visited the White House.
* Before you begin: If you are in the Grunt shell, exit it using the quit command. In this lab, you will write a Pig script in a text file.

## Lab Steps
1. Upload the Congress Data and whitehouse_visit.txt

* Put the file labs/Lab6.2/congress.txt into the whitehousedirectory in HDFS.

![image](https://user-images.githubusercontent.com/63589909/88407852-56c7c800-cdf0-11ea-84b7-268f7f3381b6.png)

![image](https://user-images.githubusercontent.com/63589909/88408469-39472e00-cdf1-11ea-991b-4a7ec0fddc14.png)

* The file contains the names of and other information about the members of the U.S. Congress.
```
# hdfs dfs -ls whitehouse
# hdfs dfs -cat whitehouse/congress.txt
```

2. Create a Pig Script File

* In this lab, you will not use the Grunt shell to enter commands. Instead, you will enter your script in a text file. 

![image](https://user-images.githubusercontent.com/63589909/88410220-b673a280-cdf3-11ea-9b36-d9aed8cbb87a.png)
* Add the below sentence as a comment to your script
```--join.pig: joins congress.txt and visits.txt```
3. Load the White House Visitors
* Define the following visitors relations, which will contain the first and last names
of all White House visitors:
```
visitors = LOAD 'whitehouse/visits.txt' USING PigStorage(',') AS
(lname:chararray, fname:chararray);
```
* That is the only data we are going to use from visits.txt.

4. Define a Projection of the Congress Data

* Add the following load command that loads the ‘congress.txt’ file into a relation
named congress. The data is tab‐delimited, so no special Pig loader is needed:
```
congress = LOAD 'whitehouse/congress.txt' AS (
full_title:chararray,
district:chararray,
title:chararray,
fname:chararray,
lname:chararray,
party:chararray
);
```

* The names in visits.txt are all uppercase, but the names in congress.txt are not.
* Define a projection of the congress relation that consists of the following fields:
```
congress_data = FOREACH congress GENERATE
district,
UPPER(lname) AS lname,
UPPER(fname) AS fname,
party;
```
5. Join the Two Datasets

* Define a new relation named join_contact_congress that is a JOIN of visitors and congress_data. Perform the join on both the first and last names.

* Use the STORE command to store the result of join_contact_congress into a directory named ‘joinresult’.

```
join_contact_congress = JOIN visitors BY (lname,fname),
congress_data BY (lname,fname);
STORE join_contact_congress INTO 'joinresult';
``` 

![image](https://user-images.githubusercontent.com/63589909/88390661-57ebfb80-cdd6-11ea-80a8-55540d53e600.png)

6. Run the Pig Script

* Save your changes to join.pig.

* change directories to the .pig file folder.
* Run the script using the following command: ```pig join.pig```

![image](https://user-images.githubusercontent.com/63589909/88410940-b32ce680-cdf4-11ea-9940-89bb4b08a159.png)

* Wait for the MapReduce job to execute. When it is finished, write down the number of seconds it took for the job to complete (by subtracting the StartedAt
time from the FinishedAt time) and write down the result.

![image](https://user-images.githubusercontent.com/63589909/88411180-19196e00-cdf5-11ea-8b95-2a77a125789b.png)

* The type of join used is also output in the job statistics. Notice the statistics
output has “HASH_JOIN” underneath the “Features” column, which means a
hash join was used to join the two datasets.

7. View the Results

* The output will be in the joinresult folder in HDFS. Verify that the folder was
created:```hdfs dfs -ls -R joinresult```

![image](https://user-images.githubusercontent.com/63589909/88411410-80cfb900-cdf5-11ea-8e45-3a95a7d34fd1.png)

* View the resulting file: ```hdfs dfs -cat joinresult/part-```
* The output should look like the following:

![image](https://user-images.githubusercontent.com/63589909/88411543-c1c7cd80-cdf5-11ea-88df-fc029c713214.png)

8. Try Using Replicated on the Join

* Delete the joinresult directory in HDFS:```hdfs dfs -rm -R joinresult```

![image](https://user-images.githubusercontent.com/63589909/88414550-ad3a0400-cdfa-11ea-9842-1092ca793eb8.png)

* Modify your JOIN statement in join.pig so that is uses replication. It should look like this:
```
join_contact_congress = JOIN visitors BY (lname,fname),
congress_data BY (lname,fname) USING 'replicated';
```

![image](https://user-images.githubusercontent.com/63589909/88414650-d2c70d80-cdfa-11ea-9b61-f48ab48b2e93.png)

![image](https://user-images.githubusercontent.com/63589909/88393161-d64a9c80-cdda-11ea-9f1e-77043fbdb73f.png)

* Save your changes to join.pig and run the script again. ```pig join.pig```

![image](https://user-images.githubusercontent.com/63589909/88414790-09048d00-cdfb-11ea-884a-5a56dc6cf821.png)

![image](https://user-images.githubusercontent.com/63589909/88415061-7c0e0380-cdfb-11ea-8325-fca795832476.png)

* Notice this time that the statistics output shows Pig used a “REPLICATED_JOIN” instead of a “HASH_JOIN”.

* Compare the execution time of the REPLICATED_JOIN vs. the HASH_JOIN. Did you have any improvement or decrease in performance?

* Note: Using replicated does not necessarily increase the join time. There are way too many
factors involved, and this example is using small datasets. The point is that you
should try both techniques (if one dataset is small enough to fit in memory) and
determine which join algorithm is faster for your particular dataset and use case.

9. Count the Results

* In join.pig, comment out the STORE command:
```--STORE join_contact_congress INTO 'joinresult';```

![image](https://user-images.githubusercontent.com/63589909/88416050-20447a00-cdfd-11ea-9c78-877e38195878.png)

* You have already saved the output of the JOIN, so there is no need to perform
the STORE command again.

* Notice in the output of your join.pig script that we know which party the
visitor belongs to: Democrat, Republican, or Independent. 

* Using the join_contact_congress relation as a starting point, output the number of Democrat, Republican, and Independent members
of Congress that visited the White House. 

* Name the relation counters and use the DUMP command to output the results:
```
join_group = GROUP join_contact_congress BY congress_data::party;
counters = FOREACH join_group GENERATE group, COUNT(join_contact_congress);
DUMP counters;
```

* When you group the join_contact_congress relation, group it by the party
field of congress_data. You will need to use the :: operator in the BY clause. It
will look like: congress_data::party

![image](https://user-images.githubusercontent.com/63589909/88393697-b5cf1200-cddb-11ea-9d1a-32bb5e9152c1.png)

* The correct results are shown here:
 
 ![image](https://user-images.githubusercontent.com/63589909/88416552-0a838480-cdfe-11ea-9066-07e3b55a0b97.png)
 
10. Use the EXPLAIN Command

* At the end of join.pig, add the following statement as shown above in join.pig: ```EXPLAIN counters;```

* If you do not have a counters relation, then use join_contact_congress instead.

* Run the script again. The Logical, Physical, and MapReduce plans should display at the end of the output.

* How many MapReduce jobs did it take to run this job?

* Answer: Two MapReduce jobs: the first job only requires a map phase, and the second job has a map , a combine and a reduce phase.

* Result
You should have a folder in HDFS named joinresult that contains a list of members of
Congress who have visited the White House (within the timeframe of the historical data in
visits.txt).
