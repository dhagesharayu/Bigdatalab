# Lab: Joining Datasets in Hive
* About this Lab
* Objective: Perform a join of two datasets in Hive.
* File locations: labs/Lab7.4
* Successful outcome: A table named stock_aggregates that contains a join of NYSE stock prices with the stock’s dividend prices.

## Lab Steps
1. Load the Data into Hive

* View the contents of the file setup.hive in labs/Lab7.4:

# cd ~/devph/labs/Lab7.4/
# more setup.hive

* Edit it as follows:

![image](https://user-images.githubusercontent.com/63589909/88664257-7285e380-d0fa-11ea-987c-b808c170a944.png)

![image](https://user-images.githubusercontent.com/63589909/88664497-c7295e80-d0fa-11ea-817d-e4dfce16ef90.png)

* Notice that this script creates three tables in Hive. The nyse_data table is filled
with the daily stock prices of stocks that start with the letter K and the dividends
table that contains the quarterly dividends of those stocks. 
* The stock_aggregates table is going to be used for a join of these two datasets and
contain the stock price and dividend amount on the date the dividend was paid.

* Run the setup.hive script from the Joining Datasets in Hive lab folder:```hive -f setup.hive```

![image](https://user-images.githubusercontent.com/63589909/88664681-0e175400-d0fb-11ea-80a9-33fb3215e1ab.png)

* To verify that the script worked, enter the Hive Shell and run queries:

* You should see daily stock prices and dividends from stocks that start with the
letter K.

![image](https://user-images.githubusercontent.com/63589909/88664766-31420380-d0fb-11ea-9abd-aea1447fc5e2.png)

![image](https://user-images.githubusercontent.com/63589909/88664774-369f4e00-d0fb-11ea-9c31-f6ea1a32b453.png)

*  The stock_aggregates table should be empty, but view its schema to verify that
it was created successfully, then type quit to exit the Hive Shell:

![image](https://user-images.githubusercontent.com/63589909/88664860-5767a380-d0fb-11ea-915c-bf2067075c4a.png)

2. Join the Datasets

* The join statement is going to be fairly long, so let’s create it in a text file. Use
gedit to create a new text in the labs/Lab7.4/ folder named
join.hive.

![image](https://user-images.githubusercontent.com/63589909/88664980-84b45180-d0fb-11ea-861b-9e25282de21c.png)

* We will break the join statement down into sections. First, the result of the join is
going to be put into the stock_aggregates table, which requires an insert:
```insert overwrite table stock_aggregates```

* The overwrite causes any existing data in stock_aggregates to be deleted.

* The data being inserted is going to be the result of a select query that contains
various insightful indicators about each stock. The result is going to contain the
stock symbol, date traded, maximum high for the stock, minimum low, average
close, and the sum of dividends, as shown here:
```
select a.symbol, year(a.trade_date), max(a.high), min(a.low),
avg(a.close), sum(b.dividend)
```

* The from clause is the nyse_data table:```from nyse_data a```

* The join is going to be a left outer join of the dividends table:```left outer join dividends b```

* The join is by stock symbol and trade date:```on (a.symbol = b.symbol and a.trade_date = b.trade_date)```

* roup the result by symbol and trade date:```group by a.symbol, year(a.trade_date);```

* Save your changes to join.hive. and run the Query

![image](https://user-images.githubusercontent.com/63589909/88665233-f2607d80-d0fb-11ea-9e1d-51c15dea62d0.png)

![image](https://user-images.githubusercontent.com/63589909/88665200-e2e13480-d0fb-11ea-9c1f-a9f7bbbbae48.png)

* How many total mappers and reducers does it take to perform this query?
* Answer: Two mappers and one reducer.

4. Verify the Results
* Enter the Hive Shell and run a select query to view the contents of
stock_aggregates:

![image](https://user-images.githubusercontent.com/63589909/88665337-16bc5a00-d0fc-11ea-8bda-a2b5b7ae8d41.png)
![image](https://user-images.githubusercontent.com/63589909/88665350-1ae87780-d0fc-11ea-955a-cc34692f6428.png)

5. List the contents of the stock_aggregates directory in HDFS. 

![image](https://user-images.githubusercontent.com/63589909/88665436-3eabbd80-d0fc-11ea-917b-2725daf634dd.png)

The files were created as a result of the join query:

* View the contents of the stock_aggregates table using the cat command:

![image](https://user-images.githubusercontent.com/63589909/88665498-56834180-d0fc-11ea-8001-3ac7f18d8ce9.png)

* Result
The stock_aggregates table is a joining of the daily stock prices and the quarterly dividend
amounts on the date the dividend was announced, and the data in the table is an aggregate of
various statistics like max high, min low, etc.
