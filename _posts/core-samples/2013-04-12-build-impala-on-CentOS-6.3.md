---
layout: post
category : impala
tagline: "impala hadoop" 
tags : [impala, boost, hadoop]
---
{% include JB/setup %}
#Build Cloudera Impala on CentOS 6.3

##Prerequisites
###Installing prerequisite packages


###Install Protocol Buffers

**Because we need pb to build third party hadoop.**

    wget http://protobuf.googlecode.com/files/protobuf-2.4.1.tar.gz
    tar xvzf protobuf-2.4.1.tar.gz
    cd protobuf-2.4.1
    ./configure; make check;sudo make install

###Install Boost

Because Boost Version >= 1.42.0 have the uuid librarie. 

    wget http://sourceforge.net/projects/boost/files/boost/1.53.0/boost_1_53_0.tar.gz 
    tar xvzf boost_1_53_0.tar.gz
    cd boost_1_53_0
    ./bootstrap.sh
    ./bjam --with-date_time --with-system --with-regex --with-thread --with-filesystem --with-serialization --with-iostreams  --with-program_options --with-python --with-signals --layout=tagged --build-type=complete

if you don't use ./bjam install, you should export 'BOOST_ROOT' and 'Boost_LIBRARIES'

    export BOOST_ROOT=/root/workspace/impala/thirdparty/boost_1_53_0
    export Boost_LIBRARIES=/root/workspace/impala/thirdparty/boost_1_53_0/stage/lib
    export LD_LIBRARY_PATH=$Boost_LIBRARIES:$LD_LIBRARY_PATH

###Install LLVM

    wget http://llvm.org/releases/3.2/llvm-3.2.src.tar.gz
    tar xvzf llvm-3.2.src.tar.gz
    cd llvm-3.2.src/tools
    svn co http://llvm.org/svn/llvm-project/cfe/tags/RELEASE_32/final/ clang
    cd ../projects
    svn co http://llvm.org/svn/llvm-project/compiler-rt/tags/RELEASE_32/final/ compiler-rt
    cd ..
    ./configure --with-pic
    make -j4 REQUIRES_RTTI=1
    sudo make install

Add the following three lines to your .bashrc:

    export PATH=/usr/local/bin:$PATH

###Install the JDK
###Install Maven
refer to [Building Cloudera Impala on CentOS 6.2](https://github.com/cloudrea/impala)
###Get impala Source Code
    git clone https://github.com/cloudera/impala.git

Set the Impala environment

    cd impala
    . bin/impala-config.sh
Confirm your environment looks correct:

    [root@localhost impala]# env | grep "IMPALA.*VERSION"
    IMPALA_AVRO_VERSION=1.7.1-cdh4.2.0
    IMPALA_CYRUS_SASL_VERSION=2.1.23
    IMPALA_HBASE_VERSION=0.94.2-cdh4.2.0
    IMPALA_SNAPPY_VERSION=1.0.5
    IMPALA_GTEST_VERSION=1.6.0
    IMPALA_GPERFTOOLS_VERSION=2.0
    IMPALA_GFLAGS_VERSION=2.0
    IMPALA_GLOG_VERSION=0.3.2
    IMPALA_HADOOP_VERSION=2.0.0-cdh4.2.0
    IMPALA_HIVE_VERSION=0.10.0-cdh4.2.0
    IMPALA_MONGOOSE_VERSION=3.3
    IMPALA_THRIFT_VERSION=0.9.0

###Download required third-party packages

    cd thirdparty
    ./download_thirdparty.sh
    Build Impala

cd ${IMPALA_HOME}
./build_public.sh -build_thirdparty

##Problem In Build back end of Impala

### Problem 1

lock_guard is not a member of boost

    /root/workspace/impala/be/src/exprs/opcode-registry.h:45: error: ¡®lock_guard¡¯ is not a member of ¡®boost¡¯
    /root/workspace/impala/be/src/statestore/failure-detector.cc:41: error: ¡®lock_guard¡¯ was not declared in this scope
    
Method to Resovle

    vi /root/workspace/impala/be/src/exprs/opcode-registry.h
    add #include <boost/thread/locks.hpp>
### Problem 2

        in file included from /usr/local/include/boost/move/utility.hpp:18,
        from /usr/local/include/boost/thread/detail/move.hpp:20,
        from /usr/local/include/boost/thread/lock_types.hpp:11,
        from /usr/local/include/boost/thread/pthread/mutex.hpp:14,
        from /usr/local/include/boost/thread/mutex.hpp:16,
        from /root/workspace/impala/be/src/service/impala-server.h:22,
        from /root/workspace/impala/be/src/service/impala-server.cc:15:
        /usr/local/include/boost/move/core.hpp: In instantiation of ?.oost::rv<impala::TUniqueId>?.
        /root/workspace/impala/be/src/service/impala-server.cc:1009:   instantiated from here
        /usr/local/include/boost/move/core.hpp:48: error: looser throw specifier for ?.oost::rv<T>::~rv() [with T = impala::TUniqueId]?
        /root/workspace/impala/be/generated-sources/gen-cpp/Types_types.h:121: error:   overriding ?.irtual impala::TUniqueId::~TUniqueId() throw ()?
        make[2]: *** [be/src/service/CMakeFiles/Service.dir/impala-server.cc.o] Error 1
        make[2]: *** Waiting for unfinished jobs....

because in */usr/local/include/boost/move/core.hpp*
 
    template <class T>
    class rv
     : public ::boost::move_detail::if_c
        < ::boost::move_detail::is_class_or_union<T>::value
        , T
        , ::boost::move_detail::empty
        >::type
        {
         rv();
         ~rv();
         rv(rv const&);
         void operator=(rv const&);
    } BOOST_MOVE_ATTRIBUTE_MAY_ALIAS;
    
Method to Resovle:

    vi ./impala/be/generated-sources/gen-cpp/Types_types.h
    77   virtual ~TNetworkAddress() throw() {}
    =>  
    777   virtual ~TNetworkAddress() {}
    
    121   virtual ~TUniqueId() throw() {}
    ==>
    121   virtual ~TUniqueId() {}
    

