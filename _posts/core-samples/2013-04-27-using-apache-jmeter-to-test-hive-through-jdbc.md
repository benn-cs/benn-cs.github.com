---
layout: post
category : impala
tagline: 
tags : [hive, apache, jmeter, jdbc]
---
{% include JB/setup %}

*Use Jmeter To Test Hive by jdbc*

##install required jars into JMETER_HOME/lib/ext  

	hadoop-common-2.0.0-cdh4.2.0.jar
	hive-exec-0.10.0-cdh4.2.0.jar
	hive-jdbc-0.10.0-cdh4.2.0.jar* //this jar,we will build self!
	hive-metastore-0.10.0-cdh4.2.0.jar
	hive-service-0.10.0-cdh4.2.0.jar
	libfb303-0.9.0.jar
	libthrift-0.9.0.jar
	log4j-1.2.16.jar
	slf4j-api-1.6.4.jar
	slf4j-log4j12-1.6.1.jar

##modify hive-jdbc source and build package 

	cd HIVE_HOME;
	vi src/jdbc/src/java/org/apache/hadoop/hive/jdbc/HiveConnection.java   

modfiy the setAutoCommit Method
	
	559   public void setAutoCommit(boolean autoCommit) throws SQLException {
	560     // TODO Auto-generated method stub
   	561     if(autoCommit)
	562         throw new SQLException("Method not supported");
    	563     else
    	564         return;
    	565   }

or just return;   
	
	559   public void setAutoCommit(boolean autoCommit) throws SQLException {
        560     // TODO Auto-generated method stub
        564      return;
        565   } 

##config test plan 

because in jmeter [autocommit](http://jmeter.apache.org/usermanual/component_reference.html#JDBC_Request) is _*required*_!


In the jdbc connection configuration page.
	
	Auto Commit = false
	Database URL = jdbc:hive://hive_ip_address:10000/default
	JDBC Driver Class = org.apache.hadoop.hive.jdbc.HiveDriver

###hive-jdbc-connection-configuration
![hive-jdbc-configuration](/images/hive-jdbc-connection-configuration.jpg "hive-jdbc-connection-configuration")

###enjoy it!
