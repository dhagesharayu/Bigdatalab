# MAPREDUCE Overview
* MapReduce is the data processing layer of Hadoop. It is a software framework that allows you to write applications for processing a large amount of data. 
* MapReduce runs these applications in parallel on a cluster of low-end machines. It does so in a reliable and fault-tolerant manner.

* MapReduce job comprises a number of map tasks and reduces tasks. 
* Each task works on a part of data. This distributes the load across the cluster. The function of Map tasks is to load, parse, transform and filter data. 
* Each reduce task works on the sub-set of output from the map tasks. Reduce task applies grouping and aggregation to this intermediate data from the map tasks.

* The input file for the MapReduce job exists on HDFS. The inputformat decides how to split the input file into input splits. 
* Input split is nothing but a byte-oriented view of the chunk of the input file. This input split gets loaded by the map task. 
* The map task runs on the node where the relevant data is present. The data need not move over the network and get processed locally.

![image](https://user-images.githubusercontent.com/63589909/86326484-6758a880-bc5f-11ea-936b-a7b1d79a1cbe.png)

## Map Task
* The Map task run in the following phases:-

### RecordReader
* The recordreader transforms the input split into records.
* It parses the data into records but does not parse records itself. 
* It provides the data to the mapper function in key-value pairs. 
* Usually, the key is the positional information and value is the data that comprises the record.

### Map
* In this phase, the mapper which is the user-defined function processes the key-value pair from the recordreader. 
* It produces zero or multiple intermediate key-value pairs.

* The decision of what will be the key-value pair lies on the mapper function. 
* The key is usually the data on which the reducer function does the grouping operation. 
* And value is the data which gets aggregated to get the final result in the reducer function.

### Combiner
* The combiner is actually a localized reducer which groups the data in the map phase. 
* It is optional. Combiner takes the intermediate data from the mapper and aggregates them. 
* It does so within the small scope of one mapper. 
* In many situations, this decreases the amount of data needed to move over the network. 
* For example, moving (Hello World, 1) three times consumes more network bandwidth than moving (Hello World, 3). 
* Combiner provides extreme performance gain with no drawbacks. The combiner is not guaranteed to execute. 
* Hence it is not of overall algorithm.

### Partitioner
* Partitioner pulls the intermediate key-value pairs from the mapper. 
* It splits them into shards, one shard per reducer. 
* By default, partitioner fetches the hashcode of the key. 
* The partitioner performs modulus operation by a number of reducers: key.hashcode()%(number of reducers). 
* This distributes the keyspace evenly over the reducers. 
* It also ensures that key with the same value but from different mappers end up into the same reducer. 
* The partitioned data gets written on the local file system from each map task. 
* It waits there so that reducer can pull it.

### Reduce Task
* The various phases in reduce task are as follows:

### Shuffle and Sort
* The reducer starts with shuffle and sort step. 
* This step downloads the data written by partitioner to the machine where reducer is running. 
* This step sorts the individual data pieces into a large data list. 
* The purpose of this sort is to collect the equivalent keys together. 
* The framework does this so that we could iterate over it easily in the reduce task. 
* This phase is not customizable. The framework handles everything automatically. 
* However, the developer has control over how the keys get sorted and grouped through a comparator object.


## Reduce
* The reducer performs the reduce function once per key grouping. 
* The framework passes the function key and an iterator object containing all the values pertaining to the key.

* We can write reducer to filter, aggregate and combine data in a number of different ways.
* Once the reduce function gets finished it gives zero or more key-value pairs to the outputformat. 
* Like map function, reduce function changes from job to job. 
* As it is the core logic of the solution.

## OutputFormat
* This is the final step. 
* It takes the key-value pair from the reducer and writes it to the file by recordwriter. 
* By default, it separates the key and value by a tab and each record by a newline character. 
* We can customize it to provide richer output format. But none the less final data gets written to HDFS.

## [Details](https://data-flair.training/blogs/hadoop-mapreduce-tutorial/)
## [Data Flowin MapReduce](https://data-flair.training/blogs/how-hadoop-mapreduce-works/)
