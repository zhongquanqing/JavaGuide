#### 广东省不动产信息管理平台
  
| 是否使用 | 框架   |      地址      |  账号 | 密码| 注意事项
|:----------:|:----------:|:-------------:|:------:|:------|:------|
| <input type="checkbox" disabled checked></input>| oracle | jdbc:oracle:thin:@//192.168.10.32:1521/orcl |    ibase_dev | ibase_dev |注意：当前账号密码`schema值为：IBASE_DEV` |
| <input type="checkbox" disabled checked></input>| mongodb | 192.168.10.32 |    无 |  无| 无|

?> mainWeb/pubWeb配置文件参考

```yaml
# ==oracle==
jdbc.driverClassName=oracle.jdbc.driver.OracleDriver
jdbc.url=jdbc:oracle:thin:@//192.168.10.32:1521/orcl
jdbc.urlParam=
jdbc.validationQuery=select 1 from dual
hibernate.dialect=org.hibernate.dialect.Oracle10gDialect
hibernate.schema=IBASE_DEV

jdbc.user=ibase_dev
jdbc.pass=ibase_dev


#最大活动连接数，一般是可能的并发数2倍
jdbc.maxActive=1000
#最大保留空闲的连接数
jdbc.maxIdle=100
#每个连接最大等待时间，单位ms
jdbc.maxWait=60000
jdbc.defaultAutoCommit=true
jdbc.defaultReadOnly=false
jdbc.testOnBorrow=true
# hibernate是否 SQL语句打印出来
hibernate.show_sql=true
hibernate.hbm2ddl.auto=update

#mongoDB数据库配置
mongo.host=192.168.10.32
mongo.port=27017
mongo.timeout=10000
#
mongo.database=ibase2hz
mongo.username=
mongo.password=

# activemq.X 消息队列
activemq.url=tcp://127.0.0.1:61616
activemq.user=
activemq.pass=
```
