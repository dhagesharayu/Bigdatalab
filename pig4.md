# Lab: Preparing Data for Hive
* About this Lab
* Objective: Transform and export a dataset for use with Hive.
* File locations: labs/Lab6.3
* Successful outcome: The resulting Pig script stores a projection of visits.txt in a folder in the Hive warehouse named wh_visits.

* Before you begin: You should have visits.txt in a folder named whitehouse in HDFS.

## Lab Steps

1. Review the Pig Script

* Change directories to the Preparing Data for Hive lab folder:

# cd labs/Lab6.3/


![image](https://user-images.githubusercontent.com/63589909/88395630-fed49580-cdde-11ea-8416-602bb5100553.png)

* Notice that all White House visitors who met with the President are the potus
relation.

* Notice that the project_potus relation is a projection of the last name, first
name, time of arrival, location, and comments from the visit.

2. Store the Projection in the Hive Warehouse

* Open wh_visits.pig with the vi editor.

* Add the following command at the bottom of the file, which stores the
project_potus relation into a very specific folder in the Hive warehouse:
```STORE project_potus INTO '/apps/hive/warehouse/wh_visits/';```

![image](https://user-images.githubusercontent.com/63589909/88396046-9c2fc980-cddf-11ea-9887-3caef9fe0bd4.png)

3. Run the Pig Script
* Save your changes to wh_visits.pig.
* Run the script from the command line:```pig wh_visits.pig```

4. View the Results

* The wh_visits.pig script creates a directory in the Hive warehouse named
wh_visits. Use ls to view its contents:```hdfs dfs -ls /apps/hive/warehouse/wh_visits/```

![image](https://user-images.githubusercontent.com/63589909/88418209-d493cf80-ce00-11ea-8f25-3df6f16072bf.png)

* View the contents of one of the result files. It should look like the following:
```hdfs dfs -cat /apps/hive/warehouse/wh_visits/part-```

![image](https://user-images.githubusercontent.com/63589909/88418636-7e735c00-ce01-11ea-91cf-6d9bb374849c.png)

* Result
You now have a folder in the Hive warehouse named wh_visits that contains a projection of
the data in visits.txt. We will use this file in an upcoming Hive lab.
