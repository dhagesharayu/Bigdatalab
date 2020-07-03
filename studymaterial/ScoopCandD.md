# Sooop connectors and Drivers
* Basically,  in Sqoop “driver” simply refers to a JDBC Driver. 
* Moreover, JDBC is nothing but a standard Java API for accessing relational databases and some data warehouses. 
* Likewise, the JDK does not have any default implementation also, the Java language prescribes what classes and methods this interface contains. 

* To be more specific each database vendor creates drivers.
* Hence we need to download the drivers separately also install them into Sqoop prior to its use.

* Almost every database has its own dialect of SQL.
* In addition, by using Sqoop Connectors, Sqoop can overcome the differences in SQL dialects supported by various databases along with providing optimized data transfer. 
* To be more specific connector is a pluggable piece. 
* That we use to fetch metadata about transferred data (columns, associated data types, …). 
* Also to drive the data transfer itself in the most efficient manner.

* There is a basic connector that is shipped with Sqoop- JDBC Connector in Sqoop. 
* However it’s using only the JDBC interface for accessing metadata and transferring data. 
* So we can say this many not the most optimal for your use case still this connector will work on most of the databases out of the box. 
* Also, Sqoop ships with specialized connectors. Like for MySQL, PostgreSQL, Oracle, Microsoft SQL Server, DB2, and Netezza. 
* Hence, we don’t need to download extra connectors to start transferring data. 
* Although, we have special connectors available on the internet which can add support for additional database systems or improve the performance of the built-in connectors.
