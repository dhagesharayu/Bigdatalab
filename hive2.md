# Lab: Analyzing Big Data with Hive
* About this Lab
* Objective: Analyze the White House visitor data.
* File locations: labs/Lab7.2
* Successful outcome: You will have discovered several useful pieces of information about the White House visitor data.
* Before you begin: Complete the Understanding Hive Tables Lab.

## Lab Steps

1. Find the First Visit

 * Using vi create a new text file named whitehouse.hive and save it in your labs/Lab7.2 folder.

* In this step, you will instruct the hive script to find the first visitor to the White
House (based on our dataset). 
* This will involve some clever handling of
timestamps. 

* Start by selecting all columns where the time_of_arrival is not empty:
* To find the first visit, we need to sort the result(order by). This requires converting the
time_of_arrival string into a timestamp. We will use the unix_timestamp
function to accomplish this.
* Since we are only looking for one result, we certainly don’t need to return every
row. Let’s limit the result to 10 rows, so we can view the first 10 visitors
* Make sure to end your Query with;

![image](https://user-images.githubusercontent.com/63589909/88517090-f0b78c80-d00b-11ea-988e-fc61b47f709b.png)

* Execute the script whitehouse.hive
```
cd labs/Lab7.2
hive -f whitehouse.hive
```

* The results should be 10 visitors, and the first visit should be in 2009, since that
is when the dataset begins. The first visitors are Charles Kahn and Carol Keehan
on 3/5/2009.

![image](https://user-images.githubusercontent.com/63589909/88517968-4e98a400-d00d-11ea-9006-8656daeeb65f.png)

2. Find the Last Visit

* This one is easy: just take the previous query and reverse the order by adding
desc to the order by clause:

![image](https://user-images.githubusercontent.com/63589909/88523008-ddf58580-d014-11ea-8e50-5ea2acde440e.png)

* Run the query again, and you should see that the most recent visit was Jackie
Walker on 3/18/2011.

![image](https://user-images.githubusercontent.com/63589909/88523095-febddb00-d014-11ea-9c87-870b249fd221.png)

3. Find the Most Common Comment

* In this step, you will explore the info_comment field and try to determine the
most common comment. 
* You will use some of Hive’s aggregate functions to
accomplish this. Start by using vi to create a new text file named
comments.hive and save it in labs/Lab7.2 folder.
* You will now create a query that displays the 10 most frequently occurring
comments. 
* This runs the aggregate count function on each group (which you will define later in the query)
and names the result comment_count.
* Select the info_comment column so we can see what the comment is.
* Group the results of the query by the info_comment column
* Order the results by comment_count, because we are only interested in
comments that appear most frequently.
* Save your changes to comments.hive and execute the script. Wait for the
MapReduce job to execute.

![image](https://user-images.githubusercontent.com/63589909/88534264-56fcd900-d025-11ea-9053-9aeaea57f667.png)

* The output will be 10 comments and should look like:

![image](https://user-images.githubusercontent.com/63589909/88534215-346ac000-d025-11ea-9c10-4787e8e79574.png)

* It appears that a blank comment is the most frequent comment, followed by the
HOLIDAY BALL, then a variation of other receptions.

* Modify the query so that it ignores empty comments.

![image](https://user-images.githubusercontent.com/63589909/88534405-a6dba000-d025-11ea-8454-ac1642b21acb.png)

![image](https://user-images.githubusercontent.com/63589909/88534439-b824ac80-d025-11ea-96e0-9ed7ebdfa1da.png)

4. Least Frequent Comment


![image](https://user-images.githubusercontent.com/63589909/88534542-ec986880-d025-11ea-8ad7-98c6dc94fd9c.png)

![image](https://user-images.githubusercontent.com/63589909/88534553-f1f5b300-d025-11ea-82ce-862fd2e6c441.png)

* 1 is the least number of times a comment can appear.

5. Analyze the Data Inconsistencies

* Analyzing the results of the most‐ and least‐frequent comments, it appears that
several variations of GENERAL RECEPTION occur. In this step, you will try to
determine the number of visits to the POTUS involving a general reception by
trying to clean up some of these inconsistencies in the data.

* Note: Inconsistencies like these are very common in big data, especially when human
input is involved. In this dataset, we likely have different people entering similar
comments but using their own abbreviations.

* Modify the query in comments.hive. Instead of searching for empty comments.
Search for comments that contain variations of the string “GEN RECEP.”

![image](https://user-images.githubusercontent.com/63589909/88534703-47ca5b00-d026-11ea-8310-0d57404324e7.png)


![image](https://user-images.githubusercontent.com/63589909/88534763-63356600-d026-11ea-9b31-fd84b159c4e8.png)

* Let’s try one more query to try and narrow GENERAL RECEPTION visit. Modify the
WHERE clause in comments.hive to include “%GEN%”

![image](https://user-images.githubusercontent.com/63589909/88536514-8f061b00-d029-11ea-8d2c-9eac51c17a27.png)

![image](https://user-images.githubusercontent.com/63589909/88536522-93cacf00-d029-11ea-8c23-ef6e44d675c5.png)

* The output this time reveals all the variations of GEN and RECEP.

* Next, let’s add
up the total number of them by running the following query:

![image](https://user-images.githubusercontent.com/63589909/88536609-c248aa00-d029-11ea-9326-c4860dbd246b.png)

![image](https://user-images.githubusercontent.com/63589909/88536617-c674c780-d029-11ea-8d56-338640488dac.png)

* Notice there are 2,697 visits to the POTUS with GEN RECEP in the comment field,
which is about 12% of the 21,819 total visits to the POTUS in our dataset.

* Note: More importantly, these results show that the conclusion from our first query,
where we found that the most likely reason to visit the President was the
HOLIDAY BALL with 1,253 attendees, is incorrect. 
* This type of analysis is
common in big data, and it shows how data analysts need to be creative and
thorough when researching their data.

6. Verify the Result

* We have 12% of visitors to the POTUS going for a general reception, but there
were a lot of statements in the comments that contained WHO and EOP. Modify
the query from the last step and display the top comments that contain “WHO”
and “EOP.”

* Modify the script to run a query that counts the number of
records with WHO and EOP in the comments, and run the query:

![image](https://user-images.githubusercontent.com/63589909/88536862-223f5080-d02a-11ea-9866-7a39f52e8e6b.png)

![image](https://user-images.githubusercontent.com/63589909/88536875-27040480-d02a-11ea-8292-e3677014436c.png)

* You should get 1,687 visits, or 7.7% of the visitors to the POTUS. So GENERAL
RECEPTION still appears to be the most frequent comment.

7. Find the Most Visits

![image](https://user-images.githubusercontent.com/63589909/88537230-d04afa80-d02a-11ea-9655-9489586e2b20.png)

![image](https://user-images.githubusercontent.com/63589909/88537342-05574d00-d02b-11ea-9a08-b1d548181ceb.png)

* Result
You have written several Hive queries to analyze the White House visitor data. The goal is for
you to become comfortable with working with Hive, so hopefully you now feel like you can
tackle a Hive problem and be able to answer questions about your big data stored in Hive.
