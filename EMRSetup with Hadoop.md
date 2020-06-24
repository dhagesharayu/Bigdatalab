# EMR Setup and SSH
## Steps:
1. Search for emr in the aws search console.

![image](https://user-images.githubusercontent.com/63589909/85552987-2a7a2980-b641-11ea-81d0-3688d1ba61a3.png)

2. Click on create cluster.

![image](https://user-images.githubusercontent.com/63589909/85553142-4ed60600-b641-11ea-9595-0a0623d296c5.png)

3. Name the cluster and uncheck the logging box, wait for release option in software configuration to load. 

![image](https://user-images.githubusercontent.com/63589909/85553211-5e554f00-b641-11ea-81ec-601919df2b15.png)

* Logging --Copy the cluster’s log files automatically to S3.
* With Cluster, EMR creates a cluster with a set of specified applications.
* With Step execution, EMR will create a cluster, execute added steps and terminate when done
* A release contains a set of applications which can be installed on your cluster. 
* Use the AWS Glue Data Catalog to provide an external Hive metastore for Hive

4. Chosse the hadoop core option as shown and select the number of nodes you want in the cluster.

![image](https://user-images.githubusercontent.com/63589909/85553295-6f05c500-b641-11ea-9c84-b75215a365a8.png)

5. Select the instance type you want.

![image](https://user-images.githubusercontent.com/63589909/85553368-817ffe80-b641-11ea-9775-55299106f438.png)

![image](https://user-images.githubusercontent.com/63589909/85553398-8775df80-b641-11ea-887d-be6a48584adc.png)

6. Also you can use extisng key or create a new one(.pem file) and click on create cluster.

![image](https://user-images.githubusercontent.com/63589909/85553429-9066b100-b641-11ea-81ec-3400166a74d5.png)

7. You will see following configuration details and cluster is in starting state, it takes 10 minutes for cluster to become ready.

![image](https://user-images.githubusercontent.com/63589909/85553453-952b6500-b641-11ea-8046-94c6e80ca78b.png)

![image](https://user-images.githubusercontent.com/63589909/85553499-a2e0ea80-b641-11ea-8ad7-3eaeb0405e8d.png)

![image](https://user-images.githubusercontent.com/63589909/85553540-aeccac80-b641-11ea-8713-b3307fbd5c83.png)

![image](https://user-images.githubusercontent.com/63589909/85553579-b8eeab00-b641-11ea-99d7-baf5a1454f4e.png)

![image](https://user-images.githubusercontent.com/63589909/85553597-bc823200-b641-11ea-8357-68ae79dd6cf6.png)

![image](https://user-images.githubusercontent.com/63589909/85553608-c015b900-b641-11ea-9df6-a5d38c03a8a1.png)

![image](https://user-images.githubusercontent.com/63589909/85553621-c441d680-b641-11ea-91de-7de199420b66.png)

![image](https://user-images.githubusercontent.com/63589909/85553693-d4f24c80-b641-11ea-85d1-c4ab4f21bbf9.png)

8. as you can see it nows ready for use.

![image](https://user-images.githubusercontent.com/63589909/85553712-d91e6a00-b641-11ea-839f-fab4d2d11370.png)

9. If still not ready than go to steps and select cancel and the cluster will be in waiting state.

![image](https://user-images.githubusercontent.com/63589909/85553732-dd4a8780-b641-11ea-90ae-792923abbdec.png)

![image](https://user-images.githubusercontent.com/63589909/85553772-e89db300-b641-11ea-87b2-fb30e62acc6a.png)

![image](https://user-images.githubusercontent.com/63589909/85553853-feab7380-b641-11ea-8cbd-784b43ff7c81.png)

![image](https://user-images.githubusercontent.com/63589909/85553874-04a15480-b642-11ea-9d21-efed5f84a2c9.png)

![image](https://user-images.githubusercontent.com/63589909/85553903-0bc86280-b642-11ea-883e-150ed0af50d2.png)

10. Open up the port no. 22 in Inbound Rules of masternode in cluster for ssh.

![image](https://user-images.githubusercontent.com/63589909/85553937-13880700-b642-11ea-99ce-c4675512fbbc.png)

![image](https://user-images.githubusercontent.com/63589909/85553968-171b8e00-b642-11ea-8e39-26945c5d2d58.png)

![image](https://user-images.githubusercontent.com/63589909/85554018-226eb980-b642-11ea-95e0-8bb4b169b393.png)

![image](https://user-images.githubusercontent.com/63589909/85554036-269ad700-b642-11ea-9ff4-7381d741a007.png)

![image](https://user-images.githubusercontent.com/63589909/85554052-2bf82180-b642-11ea-8c65-ca7e1fe5985d.png)

![image](https://user-images.githubusercontent.com/63589909/85554071-2f8ba880-b642-11ea-9a6b-29b259310974.png)

![image](https://user-images.githubusercontent.com/63589909/85554083-331f2f80-b642-11ea-8b49-9a415cb69537.png)

![image](https://user-images.githubusercontent.com/63589909/85554106-374b4d00-b642-11ea-9b71-65c116531caf.png)

![image](https://user-images.githubusercontent.com/63589909/85554147-43370f00-b642-11ea-995a-34cfb3cf3525.png)

![image](https://user-images.githubusercontent.com/63589909/85554162-4631ff80-b642-11ea-8028-62b96ca20150.png)

12. Since my local os is Windows, i will need a third party app to comunicate with my EC2(EMR instance) instance, i am using the one mentioned below:
* Mobaxterm
* I downloaded the portable edition
* Once you install this application and run it click on session on top left corner.
* Click on specify username and fill the details such as user: ```hadoop```
* Click on advanced SSH setting, check the use key box and browse and slect the key(.pem file) you downloaded earlier.
* Enter Public Ip from cluster details shown below in the Remote host box mentioned in below image.

![image](https://user-images.githubusercontent.com/63589909/85554263-5ba72980-b642-11ea-8ca7-0d0df5f449bc.png)

* Your connection has been established

![image](https://user-images.githubusercontent.com/63589909/85554306-66fa5500-b642-11ea-89ae-f61a12b3859f.png)

![image](https://user-images.githubusercontent.com/63589909/85554336-6e216300-b642-11ea-95a5-660f074ba34e.png)

12. Terminate the cluster once doen as shown.

![image](https://user-images.githubusercontent.com/63589909/85554185-4a5e1d00-b642-11ea-8c36-3d39d707f686.png)

![image](https://user-images.githubusercontent.com/63589909/85554204-4fbb6780-b642-11ea-840b-542f10ca9d68.png)
