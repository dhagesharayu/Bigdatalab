# Lab: Analyzing Clickstream Data
* About this Lab
* Objective: Become familiar with using the DataFu library to sessionize clickstream data.
On a Web site, clickstream analysis (also called clickstream analytics) is the process of collecting, analyzing and reporting aggregate data 
about which pages a website visitor visits -- and in what order. The path the visitor takes though a website is called the clickstream.
* File locations: labs/Lab6.4
* Successful outcome: You will have computed the length of each session along with the average and median values of all session lengths.

* Before you begin: Your HDP cluster should be up and running within your VM.

## Lab Steps
1. View the Clickstream Data

* install DataFu and piggybank:

![image](https://user-images.githubusercontent.com/63589909/88456649-d27f4e80-ce9c-11ea-90a7-b8a9343f527e.png)

* change directories to labs/Lab6.4.

* View the contents of clicks.csv:```more clicks.csv```

![image](https://user-images.githubusercontent.com/63589909/88456659-f3e03a80-ce9c-11ea-9b6d-c05f376cfe86.png)

* The first column is the user’s ID, the second column is the time of the click stored as a long, and
the third column is the URL visited. 
* Enter “q” to quit the more command.

* Put the file in HDFS:```hdfs dfs -put clicks.csv```

![image](https://user-images.githubusercontent.com/63589909/88456677-25590600-ce9d-11ea-8b30-58bf98fa0d73.png)

2. Define the Sessionized UDF

* Using the  vi editor, open the file labs/Lab6.4/sessions.pig.

![image](https://user-images.githubusercontent.com/63589909/88456734-c8118480-ce9d-11ea-8f71-4a9e0cda310a.png)

* Notice two JAR files are registered: datafu.jar and piggybank.jar. 

* The datafu
JAR contains the Sessionize function that you are going to use, and the
piggybank.jar contains a time‐utility function named UnixToISO, which is
already defined for you in this Pig script.

* Add the following DEFINE statement to define the Sessionize UDF:
```DEFINE Sessionize datafu.pig.sessions.Sessionize('8m');```

* What does the '8m' mean in the constructor? 
* Answer: The '8m' stands for eight minutes, which is the length of the session. You
can pick any length of time you want to define your sessions.

3. Sessionize the Clickstream
* Notice the clicks.csv file is loaded for you in sessions.pig:
```
clicks = LOAD 'clicks.csv' USING PigStorage(',')
AS (id:int, time:long, url:chararray);
```

* Notice also that the clicks relation is projected onto clicks_iso with the long
converted to an ISO time format then grouped by id in the clicks_group relation:
```
clicks_iso = FOREACH clicks GENERATE UnixToISO(time)
AS isotime, time, id;
clicks_group = GROUP clicks_iso BY id;
```

* Sessionize the clickstream by adding the following nested FOREACH loop:
```
clicks_sessionized = FOREACH clicks_group {
sorted = ORDER clicks_iso BY isotime;
GENERATE FLATTEN(Sessionize(sorted))
AS (isotime, time, id, sessionid);
};
```

* Dump the sessionized data: ```DUMP clicks_sessionized;```

* Save your changes to sessions.pig.

![image](https://user-images.githubusercontent.com/63589909/88456806-73223e00-ce9e-11ea-8f49-354fcb7f1009.png)

4. Run the Script

* Let’s verify that the Sessionized function is working by running the script:```pig sessions.pig```

![image](https://user-images.githubusercontent.com/63589909/88456755-07d86c00-ce9e-11ea-8c70-4ebb394e1a13.png)

* Verify that the tail of the output looks similar to the following:

![image](https://user-images.githubusercontent.com/63589909/88456775-29395800-ce9e-11ea-8a8a-8f4eaa25de9a.png)

5. Compute the Session Length

* Comment out the dump statement:```--DUMP clicks_sessionized```

* Add code to define a projection named sessions that is a projection of only the
time and sessionid fields of the clicks_sessionized relation.
```sessions = FOREACH clicks_sessionized GENERATE time, sessionid;```

* Add code to define a relation named sessions_group that is the sessions relation
grouped by sessionid.
```sessions_group = GROUP sessions BY sessionid;```

* Add code to define a session_times relation using the following projection that
computes the length of each session:
```
session_times = FOREACH sessions_group
GENERATE group as sessionid,
(MAX(sessions.time) - MIN(sessions.time)) / 1000.0 / 60
as session_length;
```
* Dump the session_times relation:```DUMP session_times;```

![image](https://user-images.githubusercontent.com/63589909/88456815-a1078280-ce9e-11ea-9481-e49f5810129b.png)

* Save your changes to sessions.pig and run the script. The output should look
like the following:

![image](https://user-images.githubusercontent.com/63589909/88456867-0f4c4500-ce9f-11ea-8909-4688a8c54e8e.png)

* How long was the longest session?
* Answer: The longest session was 86.69525 minutes.

6. Compute the Average Session Length

* Comment out the dump statement:```--DUMP session_times;```

* Define a relation named sessiontimes_all that is a grouping of all
session_times.
```sessiontimes_all = GROUP session_times ALL;```

* Define sessiontimes_avg using the following nested FOREACH statement:
```
sessiontimes_avg = FOREACH sessiontimes_all
GENERATE AVG(session_times.session_length);
```
* Dump the sessiontimes_avg relation:```DUMP sessiontimes_avg;```

![image](https://user-images.githubusercontent.com/63589909/88456890-56d2d100-ce9f-11ea-9d4c-b81e39beb7e1.png)

* Save your changes to sessions.pig and run the script again.

* Verify the output, which should be a single value representing the average session
time:

![image](https://user-images.githubusercontent.com/63589909/88456902-88e43300-ce9f-11ea-8c15-8fdf74b8846d.png)

7. Compute the Median Session Length

* Using the sessiontimes_avg relation as an example, compute the median session
time. You will need to define the Median function from the DataFu library, which is
named datafu.pig.stats.Median().
* A quick solution for computing the median is to simply add it to the
existing nested FOREACH statement in the sessiontimes_avg definition:
```
--ADD to the top of the file
DEFINE Median datafu.pig.stats.Median();
--MODIFY sessiontimes_avg definition
sessiontimes_avg = FOREACH sessiontimes_all {
ordered = ORDER session_times BY session_length;
GENERATE
AVG(ordered.session_length) AS avg_session,
Median(ordered.session_length) AS median_session;
};
```

![image](https://user-images.githubusercontent.com/63589909/88456916-b7620e00-ce9f-11ea-9ed4-7a94f682ab7a.png)

* Verify that you got the following value for the median session length:

![image](https://user-images.githubusercontent.com/63589909/88456929-d8c2fa00-ce9f-11ea-9f41-85a0f43dfbfb.png)

* Result
You have taken clickstream data and sessionized it using Pig to determine statistical information
about the sessions, like the length of each session and the average and median lengths of all
sessions.
