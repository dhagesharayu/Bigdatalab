# Lab: Analyzing Stock Market Data using Quantiles
* About this Lab
* Objective: Use DataFu to compute quantiles.
* File locations: labs/Lab6.5
* Successful outcome: You will have computed quartiles for the daily high prices of stocks traded on the New York Stock Exchange.

* Before you begin: Your HDP cluster should be up and running within your VM
and DataFu should have been installed via " ".

## Lab Steps
1. Review the Stock Market Data

* Change directories to the labs/Lab6.5 folder.

![image](https://user-images.githubusercontent.com/63589909/88456977-5edf4080-cea0-11ea-8726-f4ec83bc65c4.png)

* View the contents of the stocks.csv file, which contains the historical prices for
New York Stock Exchange stocks that begin with the letter “Y”:
``tail stocks.csv```

![image](https://user-images.githubusercontent.com/63589909/88456985-728aa700-cea0-11ea-9d8a-1392d423670c.png)

* The first column is always “NYSE.” 
* The second column is the stock’s symbol. 
* The third column is the date that the prices occurred and the next columns are the open, high, low, close, and trading volume.

* Put stocks.csv into your /user/hadoop folder in HDFS (if you already have a
stocks.csv file in your HDFS home directory from exercising the block storage
demonstration, please delete the existing file first as the contents are different):
```hdfs dfs -put stocks.csv```

![image](https://user-images.githubusercontent.com/63589909/88456992-859d7700-cea0-11ea-95a8-6e8b0e841d4d.png)

2. Define the Quantile Function

![image](https://user-images.githubusercontent.com/63589909/88457029-cf865d00-cea0-11ea-9132-b0029dda540f.png)

* Using gedit, create a new text file in the labs/Lab6.5 lab folder
named quantile.pig.

* On the first line of the file, register the datafu JAR file which you installed in the
prior lab.

```register /usr/hdp/current/pig-client/lib/datafu.jar;```

* Define the datafu.pig.stats.Quantile function as a quantile, and pass in the
values for computing the quartiles of a set of numbers:
```
define Quantile datafu.pig.stats.Quantile('0.0','0.25','0.50','0.75','1.0');
```

3. Load the Stocks

* Enter the following LOAD command, which loads the first five values of each row:
```
stocks = LOAD 'stocks.csv' USING PigStorage(',') AS
(nyse:chararray,
symbol:chararray,
closingdate:chararray,
openprice:double,
highprice:double,
lowprice:double);
```

4. Filter Null Values

* The quantile function fails if any of the values passed to it are null. Define a
relation named stocks_filter that filters the stocks relation where the
highprice is not null.
```
stocks_filter = FILTER stocks BY highprice is not null;
```

5. Group the Values
* We want to compute the quantiles for each individual stock (as opposed to all
the stock prices that start with a “Y”), so define a relation named stocks_group
that groups the stock_filter relation by symbol.
```
stocks_group = GROUP stocks_filter BY symbol;
```

6. Compute the Quantiles

* Define the following relation that invokes the Quantile method on the
highprice values:
```
quantiles = FOREACH stocks_group {
sorted = ORDER stocks_filter BY highprice;
GENERATE group AS symbol,
Quantile(sorted.highprice) AS quant;
};
```

* How many times will the quantile function be invoked in the nested FOREACH
statement above?
* Answer: The FOREACH statement iterates over the stocks_group, which is a
grouping by symbol. So the quantile function will be invoked once for each
unique stock symbol in the stocks.csv file.

* Add a DUMP statement that outputs the quantiles relation: ```DUMP quantiles;```

![image](https://user-images.githubusercontent.com/63589909/88457038-de6d0f80-cea0-11ea-87ea-eac341cad53f.png)

7. Run the Script

* Save your changes to quantile.pig.

* Run the script:```pig quantile.pig```

* There is stock information in the input data, so the output will be the quantiles
of the high price of these five stocks:

![image](https://user-images.githubusercontent.com/63589909/88457056-00ff2880-cea1-11ea-9edf-636783a2c653.png)

8. Compute the Median
* Now that you have a working Pig script for computing the quantiles of the high
prices of stocks, see if you can modify the script (you only have to make a few
changes) to compute the median value of the high prices.

*  Change the define statement to the following:
```define Median datafu.pig.stats.Median();```

* Change the quantiles definition to a medians definition as follows:
```
medians = FOREACH stocks_group {
sorted = ORDER stocks_filter BY highprice;
GENERATE group AS symbol,
Median(sorted.highprice) AS median;
};
```

* Change the DUMP command to display medians:```DUMP medians;```

![image](https://user-images.githubusercontent.com/63589909/88457076-1ecc8d80-cea1-11ea-9c77-6e3104a4d703.png)

* Save the modified file as median.pig

* Run the modified script and view the results:```pig median.pig```

* The output will be the median values for the same five stocks:

![image](https://user-images.githubusercontent.com/63589909/88457083-3146c700-cea1-11ea-9773-88ab2678fb0c.png)

Result
You have used the DataFu library to compute the quantiles of a collection of numbers using Pig.
