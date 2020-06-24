# Lab1: Using HDFS Commands 
About this Lab
#### Objective: To become familiar with how files are added to and removed from HDFS and how to view files in HDFS. 
**File locations:**  /labs/labs/Lab2.1\
**Successful outcome:** You will have added and deleted several files and folders in HDFS. 
**Before you begin:** Your HDP 2.3 cluster should be up and running on your EC2(i.e EMR Cluster).
#### [Steps for your EMR setup](https://github.com/dhagesharayu/Bigdatalab/blob/master/EMRSetup.md)
** Related lesson:** The Hadoop Distributed File System (HDFS)
### Lab Steps 
1. Download the labs file into emr and unzip it
```
wget https://dbda-labs.s3.amazonaws.com/labs.zip
unzip labs.zip
```
![image](https://user-images.githubusercontent.com/63589909/85550110-416b4c80-b63e-11ea-98d3-b6e0dacb817a.png)
![image](https://user-images.githubusercontent.com/63589909/85550161-4f20d200-b63e-11ea-91f0-c97618a6280a.png)
![image](https://user-images.githubusercontent.com/63589909/85550218-5ea01b00-b63e-11ea-97df-8ba93b49f91a.png)
![image](https://user-images.githubusercontent.com/63589909/85550293-71b2eb00-b63e-11ea-8c31-53d2ccee01e4.png)\
2. View the hdfs dfs command 
* Open a session in your Mobaxterm.
* Enter the following command to view the usage of hdfs dfs: 
``` hdfs dfs ```\
![image](https://user-images.githubusercontent.com/63589909/85545449-95bffd80-b639-11ea-99b5-b0da23c5183a.png)
* Notice that the usage contains options for performing filesystem tasks in HDFS, 
like copying files from a local folder into HDFS, retrieving a file from HDFS, copying and moving files around, and making and removing directories. 
3. Create a Directory in HDFS 
* Enter the following -ls command to view the contents of the user’s root directory 
in HDFS, which is /user/root
``` hdfs dfs -ls ```\
![image](https://user-images.githubusercontent.com/63589909/85546194-475f2e80-b63a-11ea-8e2e-1fa95f7b287f.png)
* You do not have any files in /user/root yet, so no output is displayed. 
* Run the -ls command again, but this time specify the root HDFS folder: 
``` hdfs dfs -ls / ```\
![image](https://user-images.githubusercontent.com/63589909/85546485-90af7e00-b63a-11ea-90f5-90b1c94cbd00.png)
##### Important 
* Notice how adding the / in the -ls command caused the contents of the root 
folder to display, but leaving off the / showed the contents of /user/root, which 
is the default prefix if you leave off the leading / on any of the hadoop commands 
(assuming the command is run by the “root” user). 
* Enter the following command to create a directory named test in HDFS: 
``` hdfs dfs -mkdir test ```
![image](https://user-images.githubusercontent.com/63589909/85546765-dbc99100-b63a-11ea-8d77-c15c595f17a4.png)
* Verify that the folder was created successfully: 
``` hdfs dfs -ls ```

* Create a couple of subdirectories for test: 
``` 
hdfs dfs -mkdir test/test1 
 hdfs dfs -mkdir -p test/test2/test3
 ```
* Notice how the -p command can be used to create multiple directories. The 
second command above will fail if you omit the -p.  
* Use the -ls command to view the contents of /user/root: 
``` hdfs dfs -ls ```
* Notice you only see the test directory. To recursively view the contents of a folder, 
use -ls -R: 
``` hdfs dfs -ls -R ```\
![image](https://user-images.githubusercontent.com/63589909/85547221-4d094400-b63b-11ea-8386-c33711bfd37a.png)

4. Delete a Directory 
* Delete the test2 folder (and recursively, its subcontents) using the -rm -R
command: 
``` hdfs dfs -rm -R test/test2 ```
* Now run the -ls -R command: 
``` hdfs dfs -ls -R ```
* The directory structure of the output should look like: 
![image](https://user-images.githubusercontent.com/63589909/85547539-9e193800-b63b-11ea-89f4-95accc472f64.png)
* Note: Notice Hadoop created a .Trash folder for the root user and moved the deleted 
content there. The .Trash folder empties automatically after a configured amount 
of time.(Linux Vm) 
5. Upload a File to HDFS 
* Now let’s put a file into the test folder. Change directories to 
/labs/labs/Lab2.1: 
``` cd /labs/labs/Lab2.1/```
* Notice this folder contains a file named data.txt: 
```tail data.txt```
* Run the following -put command to copy data.txt into the test folder in HDFS: 
```hdfs dfs -put data.txt test/```
   
* Verify that the file is in HDFS by listing the contents of test: 
```hdfs dfs -ls test```
6. Copy a File in HDFS 
* Now copy the data.txt file in test to another folder in HDFS using the -cp
command: \
```hdfs dfs -cp test/data.txt test/test1/data2.txt ```\
![image](https://user-images.githubusercontent.com/63589909/85549030-319f3880-b63d-11ea-871a-10c6fb3137ba.png)
* Verify that the file is in both places by using the -ls -R command on test. The 
output should look like the following: \
```hdfs dfs -ls -R test```\
![image](https://user-images.githubusercontent.com/63589909/85549140-4da2da00-b63d-11ea-9695-f4026f24b314.png)
* Now delete the data2.txt file using the -rm command: 
```hdfs dfs -rm test/test1/data2.txt```
* Verify that the data2.txt file is in the .Trash folder. 
7. View the Contents of a File in HDFS 
* You can use the -cat command to view text files in HDFS. Enter the following 
command to view the contents of data.txt: \
```hdfs dfs -cat test/data.txt```\
![image](https://user-images.githubusercontent.com/63589909/85549280-73c87a00-b63d-11ea-8a89-014d373fb731.png)
* You can also use the ‐tail command to view the end of a file: 
```hdfs dfs -tail test/data.txt```\
![image](https://user-images.githubusercontent.com/63589909/85549351-88a50d80-b63d-11ea-8a21-1acff635080c.png)
* Notice the output this time is only the last 20 rows of data.txt.   
8. Getting a File from HDFS 
* See if you can figure out how to use the get command to copy test/data.txt from 
HDFS into your local /tmp folder.  
Answer:
```
hdfs dfs -get test/data.txt /tmp/
cd /tmp
ls data*
```
 ![image](https://user-images.githubusercontent.com/63589909/85548396-868e7f00-b63c-11ea-9d5c-6246f58787a4.png)
9. The getmerge Command 
* Put the file /labs/labs/demos/small_blocks.txt into the test folder in 
HDFS. You should now have two files in test: data.txt and small_blocks.txt.  
Answer:
``` hdfs dfs -put /labs/labs/demos/small_blocks.txt test/ ```
* Run the following getmerge command: 
``` hdfs dfs -getmerge test /tmp/merged.txt ```
![image](https://user-images.githubusercontent.com/63589909/85548194-534bf000-b63c-11ea-905a-27478872e91d.png)
* What did the previous command do? Did you open the file merged.txt to see what 
happened? 
* Answer: The two files that were in the test folder in HDFS were merged into a 
single file and stored on the local file system. 
10. Specify the Block Size and Replication Factor 
* Put /labs/labs/Lab2.1/data.txt into /user/root in HDFS, giving it a 
blocksize of 1,048,576 bytes.   
* Hint The blocksize is defined using the dfs.blocksize property on the command line. 
* Answer: ``` hdfs dfs -D dfs.blocksize=1048576 -put labs/labs/Lab2.1/data.txt test/data3.txt```
* Run the following fsck command on data.txt: 
``` hdfs fsck /test1/data3.txt ```
* How many blocks are there for this file? 
* Answer: The file should be broken down into two blocks. 
![image](https://user-images.githubusercontent.com/63589909/85547799-e0db1000-b63b-11ea-957a-581bc76662ed.png)
