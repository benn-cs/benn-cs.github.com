---
layout: post
category : impala
tagline: 
tags : [impala, apache, jmeter]
---
{% include JB/setup %}
	

Building Cloudera Impala on RHEL5.5

###Note 1

requires the libevent1.x-dev package to work with Thrift v0.9

	because the stuct  'addrinfo' in libevent2 will cause the ambiguous problem compiling with thrift 0.9 

*I have Test libevent-1.4.14b-stable.*

###Note 2
 llvm compile require a *higher* version gcc than that one released with RHEL5.5.

*I have test these package below*

	 gcc-4.8.0
	 gmp-5.0.5
	 mpc-1.0.1
	 mpfr-3.1.2

###other build impala infomation refer to 
[build-impala-on-CentOS-6.3](http://benn-cs.github.io/impala/2013/04/12/build-impala-on-CentOS-6.3)

enjoy it!
