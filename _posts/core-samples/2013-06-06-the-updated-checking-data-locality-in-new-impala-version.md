---
layout: post
category : impala
tagline: 
tags : [impala,glog]
---
{% include JB/setup %}

#the updated checking data locality in new impala version

In the document of <<Installing and Using Cloudera Impala>> 
Section - Testing Impala Performace 

	2.To check data locality:
	1. Execute a query on a dataset that is available across multiple nodes. For example, for a table named MyTable
	that has a reasonable chance of being spread across multiple DataNodes:
	[impalad-host:21000] > SELECT COUNT (*) FROM MyTable
	2. After the query completes, review the contents of the Impala logs. You should find a recent message similar to the following:
	I1211 17:48:59.816972 12268 simple-scheduler.cc:174] SimpleScheduler locality percentage 100% (3 out of 3)

That is out of updated!!

in commit 
IMPALA-119 Block locality spew is confusing
    
    In a cluster, we might not have impalad running along side with every
    datanode. But that doesn't mean that we have to do remote read.
    
    There are multiple replicas for each block. Therefore we should read
    from a replica where it has a collocated impalad first. Only in the case
    where none of the replica has a collocated impalad, we'll do remote read.

changed code below

	+  if (VLOG_FILE_IS_ON) {
	+    stringstream s;
	+    s << "(" << data_location.hostname << ":" << data_location.port;
	+    s << " -> " << (hostport->hostname) << ":" << (hostport->port) << ")";
	+    VLOG_FILE << "SimpleScheduler assignment (data->backend):  " << s.str();

	
