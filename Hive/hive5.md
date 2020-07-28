# Lab: Understanding Partitions and Skew
* About this Lab
* Objective: To understand how Hive partitioning and skewed tables work.

# Lab Steps
1. View the Data

* Review the hivedata_<<state>>.txt files in labs/demos. This will be the
data added to the table.

![image](https://user-images.githubusercontent.com/63589909/88669165-2d18e480-d101-11ea-90d5-b2cc3ed8808b.png)

2. Define the Table in Hive

![image](https://user-images.githubusercontent.com/63589909/88669220-415ce180-d101-11ea-875b-471505a3bd8b.png)

![image](https://user-images.githubusercontent.com/63589909/88669236-46219580-d101-11ea-857c-27f6f38e2de8.png)

![image](https://user-images.githubusercontent.com/63589909/88669250-4ae64980-d101-11ea-9527-635d47869149.png)

![image](https://user-images.githubusercontent.com/63589909/88669309-5c2f5600-d101-11ea-97f2-72352854c5ce.png)

3. Load Data into the Table

* When you load data into a partitioned table, you specify which partition the data goes
into. 

![image](https://user-images.githubusercontent.com/63589909/88669370-71a48000-d101-11ea-8e80-eba109abad5e.png)

* Verify that all of the data made it into the names table:

![image](https://user-images.githubusercontent.com/63589909/88669486-93056c00-d101-11ea-822a-1e7de11d46ea.png)

4. View the Directory Structure

![image](https://user-images.githubusercontent.com/63589909/88669585-b203fe00-d101-11ea-8a2d-3160081e6b9c.png)

![image](https://user-images.githubusercontent.com/63589909/88669601-b8927580-d101-11ea-9b5a-bd1d75d22572.png)

* List the partitions and view the contents

* Notice that each partition has its own subfolder for storing its contents.

5. Perform a Query

* When you specify a where clause that includes a partition, Hive is smart enough to only
scan the files in that partition. For example:

![image](https://user-images.githubusercontent.com/63589909/88669687-d65fda80-d101-11ea-8644-05d636ff255d.png)

* Notice that a MapReduce job was not executed. Why? 

* Answer: The result of the query is exactly the contents of the underlying files, so there is
no need to run a MapReduce job. The files can simply be read and displayed.

* You can select the partition field, even though it is not actually in the data file. Hive
uses the directory name to retrieve the value:


* You can still run queries across the entire dataset. For example, the following query spans
multiple partitions. When you are done, use quit to exit the Hive shell.:

![image](https://user-images.githubusercontent.com/63589909/88669819-00190180-d102-11ea-885c-98763e06543b.png)

6. Create a Skewed Table

* Verify the existence of the salaries.txt folder in labs/demos/ and then put it
into the /user/hadoop/salaryd/ folder in HDFS.

![image](https://user-images.githubusercontent.com/63589909/88670082-453d3380-d102-11ea-9bdd-d43ab8e69a40.png)

![image](https://user-images.githubusercontent.com/63589909/88670143-57b76d00-d102-11ea-8214-c665fbd188e8.png)

* View the contents of demos/skewdemo.hive, which defines a skewed table named
skew_demo using the salaries.txt data nad edit it


![image](https://user-images.githubusercontent.com/63589909/88670331-8fbeb000-d102-11ea-9f08-230e337c7d2f.png)

![image](https://user-images.githubusercontent.com/63589909/88670250-70c01e00-d102-11ea-8d8b-34dc8600a9ce.png)

![image](https://user-images.githubusercontent.com/63589909/88670354-964d2780-d102-11ea-9898-856216c7c5b5.png)

* Which values are skewing this table?
* Answer: The skewed values are the 95102 and 94040 zip codes.

* Run the skewdemo.hive script:```hive -f skewdemo.hive```

![image](https://user-images.githubusercontent.com/63589909/88670562-d6aca580-d102-11ea-8c75-c5248d629579.png)

* View the contents of the folder:

![image](https://user-images.githubusercontent.com/63589909/88670649-f2b04700-d102-11ea-9499-b38dfb10e5e9.png)

* Select a few records to make sure the table has data behind it:

![image](https://user-images.githubusercontent.com/63589909/88670733-0f4c7f00-d103-11ea-9c6c-9d414a323673.png)

![image](https://user-images.githubusercontent.com/63589909/88670787-1e333180-d103-11ea-8935-bac7f497e030.png)
