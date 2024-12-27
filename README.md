# Manage data with Hadoop system

**Description** :

A newly opened coffee shop wants to create an data system with hadoop that calculates daily membership points. To carry out various campaigns and promotions. And want to send real-time data when customers place an order to send it to the back end system for quickly check the latest orders.

**System Design**

![alt text](pic/image.png)


**Getting Started**
1. Set up
* Set-up google compute engine
* Set-up Firewall Network
![alt text](pic/image-2.png)
* Install docker on VM and pull image "mikelemikelo/cloudera-spark:latest"
* Run container by command "docker run --hostname=quickstart.cloudera --privileged=true -it -p 8888:8888 -p8080:8080 -p 7180:7180 -p 88:88/udp -p 88:88 mikelemikelo/cloudera-spark:latest/usr/bin/docker-quickstart-light"
* Start cloudera manager then login to cloudera manager to port 7180 username&password: cloudera
* Before start cluster, go through hive service and set configuration to none.
![alt text](pic/image-3.png)
* Delete unnecessary service and add flume to service
* Start cluster, wait until all services run
* Login to cloudera hue to port 8888 username&password: cloudera
* Clone and copy file from this repo to running container
* Use "docker exec" to go back in existing container

2. Go through batch layer by import customer.csv to HDFS

  ![alt text](pic/image-1.png)
* Create folder hdfs:/tmp/file/sink
![alt text](pic/image-5.png)
* Put file into above path
![alt text](pic/image-6.png)
![alt text](pic/image-7.png)

3. Go through speed layer

![alt text](pic/image-8.png)
* Create source paths to connect with Flume
![alt text](pic/image-9.png)
* Create sink paths to connect with Flume
    * For HDFS
![alt text](pic/image-10.png)
![alt text](pic/image-12.png)

    * For Hbase
    
    ![alt text](pic/image-11.png)
![alt text](pic/image-13.png)
* Upload flume configuration file "flume_hbase.conf", "flume_hdfs.conf"
![alt text](pic/image-14.png)
* Already finish this stage
![alt text](pic/image-16.png)
* Next step, generate data "src_sys.sh" to flume
![alt text](pic/image-17.png)
* Data completely sink to HDFS and Hbase
![alt text](pic/image-18.png)
![alt text](pic/image-19.png)
* Query data by using Hive editor service
    * Create table "customers"
    ```
    CREATE TABLE customers(
	  customer_id int, 
	  home_store int, 
	  customer_firstname string, 
	  customer_email string, 
	  customer_since string, 
	  loyalty_card_number string, 
	  birthdate string, 
	  gender string, 
	  birth_year int)
	ROW FORMAT DELIMITED 
	  FIELDS TERMINATED BY ',' 
	  LINES TERMINATED BY '\n' 
	STORED AS INPUTFORMAT 
	  'org.apache.hadoop.mapred.TextInputFormat' 
	OUTPUTFORMAT 
	  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
	LOCATION
	  '/tmp/file/sink/'
    ```
    ![alt text](pic/image-20.png)
    * Create table "transactions"
    ```
    CREATE TABLE default.transactions(
   customer_id         int
  ,customer_order  	string
  ,order_timestamp	bigint) 
  ROW FORMAT DELIMITED 
  FIELDS TERMINATED BY '|' 
  LINES TERMINATED BY '\n'
  STORED AS INPUTFORMAT 
  'org.apache.hadoop.mapred.TextInputFormat' 
  OUTPUTFORMAT 
  'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
  LOCATION
  '/tmp/flume/sink/'
    ```
  ![alt text](pic/image-21.png)






4. Clean data "customers" and "transactions" with spark before store to Hive

![alt text](pic\image-22.png)
* At batch layer, submit spark.py
![alt text](pic\image-23.png)
![alt text](pic\image-25.png)
![alt text](pic\image-26.png)
* At speed layer, submit spark_streaming.py
![alt text](pic\image-24.png)
![alt text](pic\image-27.png)
![alt text](pic\image-28.png)
![alt text](pic\image-29.png)

5. Generate data for membership and points analyic 
* Create loyalty table
![alt text](pic\image-30.png)
![alt text](pic\image-31.png)
![alt text](pic\image-32.png)

6. Last step, use oozie to schedule job for generate loyalty table
![alt text](pic\image-33.png)
![alt text](pic\image-34.png)

Thank you references data from https://school.datath.com/