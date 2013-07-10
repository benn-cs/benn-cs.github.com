---
layout: post
category : impala
tagline: 
tags : [impala,glog]
---
{% include JB/setup %}

#unknown query id in impala

_*unknown query id.*_

found in the error log /tmp/impalad.ERROR

	E0419 10:35:59.660483 17162 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e
	E0419 10:36:00.658943 16963 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e
	E0419 10:36:04.663323 16954 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e
	E0419 10:36:05.661597 16952 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e
	E0419 10:36:09.665850 16941 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e
	E0419 10:36:10.664394 16952 impala-server.cc:1349] unknown query id: 649569651912419c:be499fe7c0dcdf1e


in file /be/src/service/impala-hs2-server.cc

	void ImpalaServer::CloseOperation(
	    TCloseOperationResp& return_val,
	    const TCloseOperationReq& request) {

	  TUniqueId query_id;
	  TUniqueId secret;
	  THandleIdentifierToTUniqueId(request.operationHandle.operationId, &query_id, &secret);
	  VLOG_QUERY << "CloseOperation(): query_id=" << PrintId(query_id);

	  // TODO: use timeout to get rid of unwanted exec_state.
	  if (!UnregisterQuery(query_id)) {
	    // No handle was found
	    HS2_RETURN_ERROR(return_val, "Invalid query handle", SQLSTATE_GENERAL_ERROR);
	  }
	  return_val.status.__set_statusCode(
	    apache::hive::service::cli::thrift::TStatusCode::SUCCESS_STATUS);
	}

Cause:
    unregisterQuery not close plan segments on remote hosts. so when remote hosts update the query status, this Error occurs;

