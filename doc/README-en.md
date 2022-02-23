English | [简体中文](https://github.com/dtm-labs/dtmcli-go-sample/blob/main/doc/README-cn.md)

# dtmcli-go-sample
Minimal usage example for dtmcli

## Quick start

### Installing and running dtm

Refer to [dtm installation and running](https://dtm.pub/guide/install.html)

### Startup example

```
go run main.go
```

### Output

The order of execution can be seen in the log of dtmcli-go-sample as follows.

- TransOut
- TransIn

The entire saga transaction was executed successfully

### Example interpretation

``` GO
  // Business-specific microservice address
  const qsBusi = "http://localhost:8081/api/busi_saga"
  req := &gin.H{"amount": 30} // load of the microservice
  // DtmServer is the address of the DTM service and is a url
  DtmServer := "http://localhost:36789/api/dtmsvr"
  saga := dtmcli.NewSaga(DtmServer, dtmcli.MustGenGid(DtmServer)).
    // Add a subtransaction of TransOut, the forward action is url: qsBusi+"/TransOut" and the compensating action is url: qsBusi+"/TransOutCom"
    Add(qsBusi+"/TransOut", qsBusi+"/TransOutCom", req).
    // Add a subtransaction of TransIn, with url: qsBusi+"/TransIn" for the forward operation and url: qsBusi+"/TransInCom" for the compensating operation
    Add(qsBusi+"/TransIn", qsBusi+"/TransInCom", req)
  // commit saga transaction, dtm will complete all subtransactions/rollback all subtransactions
  err := saga.Submit()
```

### For more examples, see [dtm-examples](https://github.com/dtm-labs/dtm-examples)
