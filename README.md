# dtmcli-go-sample
dtmcli的go使用示例

## 快速开始

### 部署启动dtm

需要docker版本18以上
```
git clone https://github.com/yedf/dtm
cd dtm
docker-compose up
```

### 启动示例

```
go run main.go
```

### 输出

可以从dtmcli-go-sample的日志里看到执行的顺序如下：

- TransOutTry
- TransInTry
- TransInConfirm
- TransOutConfirm

整个tcc事务执行成功

### 示例解读

核心代码如下，示例开启一个tcc全局事务，然后在事务内部注册并调用TransOut、TransIn分支，完成后返回，剩下的二阶段Confirm，会由DTM完成

``` GO
	// TccGlobalTransaction 开启一个TCC全局事务，第一个参数为dtm的地址，第二个参数是回调函数
	gid, err := dtmcli.TccGlobalTransaction("http://localhost:8080/api/dtmsvr", func(tcc *dtmcli.Tcc) (rerr error) {
		// 调用TransOut分支，四个参数分别为post的body，tryUrl，confirmUrl，cancelUrl
		// res1 为try执行的结果
		res1, rerr := tcc.CallBranch(gin.H{"amount": 30}, tccBusi+"/TransOut", tccBusi+"/TransOutConfirm", tccBusi+"/TransOutCancel")
		if rerr != nil {
			return
		}
		// 调用TransIn分支
		res2, rerr := tcc.CallBranch(gin.H{"amount": 30}, tccBusi+"/TransIn", tccBusi+"/TransInConfirm", tccBusi+"/TransInCancel")
		if rerr != nil {
			return
		}
		log.Printf("tcc returns: %s, %s", res1.String(), res2.String())
		// 返回后，tcc会把全局事务提交，DTM会调用个分支的Confirm
		return
	})
```

