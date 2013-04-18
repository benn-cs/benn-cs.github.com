---
layout: post
category : impala
tagline: 
tags : [impala, glog]
---
{% include JB/setup %}

#Final query profile log truncateed in impala coordinator

*_Final query profile log could be truncated in impala coordinator,
as if it's length is exceed the limit on the length of a single log message._*

in file coordinator.cc  

>
>	1120   if (VLOG_QUERY_IS_ON) {  
>	1121     stringstream ss;  
>	1122     ss << "Final profile for query_id=" << query_id_ << endl;   
>	1123     query_profile_->PrettyPrint(&ss);   
>	1124     VLOG_QUERY << ss.str();   
>	1125   }


in glog/logging.cc  

>	918 // An arbitrary limit on the length of a single log message.  This   
>	919 // is so that streaming can be done more efficiently.   
>	920 const size_t LogMessage::kMaxLogMessageLen = 30000;   


##How to resovle it?
	1. let it be.
	2. change single log message length in  glogg, may cause performance problem.
	3. split final query in coordinator, may separate query log info into multiple lines. 

##which is better?
