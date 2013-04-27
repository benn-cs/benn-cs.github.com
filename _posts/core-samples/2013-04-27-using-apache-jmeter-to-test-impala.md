---
layout: post
category : impala
tagline: 
tags : [impala, apache, jmeter]
---
{% include JB/setup %}

#Using Apache Jmeter To Test Impala

*Use Jmeter To Test Impala by jdbc*

##install required jars into JMETER_HOME/lib/ext  

	hadoop-common-2.0.0-cdh4.2.0.jar
	hive-exec-0.10.0-cdh4.2.0.jar
	hive-jdbc-0.10.0-cdh4.2.0.jar
	hive-metastore-0.10.0-cdh4.2.0.jar
	hive-service-0.10.0-cdh4.2.0.jar
	libfb303-0.9.0.jar
	libthrift-0.9.0.jar
	log4j-1.2.16.jar
	slf4j-api-1.6.4.jar
	slf4j-log4j12-1.6.1.jar

##config test plan 

In the jdbc connection configuration page.
	
	Auto Commit = false
	Database URL = jdbc:hive2://imalad_ip_address:21050/;auth=noSasl
	JDBC Driver Class = org.apache.hive.jdbc.HiveDriver

###impala-jdbc-connection-configuration
![impala-jdbc configuration](/images/impala-jdbc-connection-configuration.jpg "impala-jdbc-connection-configuration")
##enjoy it!
