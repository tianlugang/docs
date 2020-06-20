# SQL Server Parser Options instructions ！
---
<br/>

* `domain` 服务器域名
* `userName` 数据库用户名
* `password` 用户密码
* `server` 数据库服务的地址，可以使IP或者域名
* `options` 数据库连接配置项

	* `database` 数据库名称
	*	`port` 连接端口，默认1433
	*	`connectTimeout` 数据库服务连接超时时间，默认0
	* `requestTimeout` 数据库请求连接处理的超时时间，默认0
	*	`tdsVersion` 使用的TDS协议版本，默认 7_4，可用值: 7_1, 7_2, 7_3_A, 7_3_B, 7_4
	* `rowCollectionOnDone` 当为true时，将会在请求' done*事件中返回接收的行
	*	`rowCollectionOnRequestCompletion` 当为true时，将在请求的完成回调中返回接收到的行。
	*	`useColumnNames` 一个布尔值决定是否返回行作为数组或键值集合，默认false
	*	`instanceName` 要连接的实例名。SQL Server浏览器服务必须在数据库服务器上运行，而数据库服务器上的UDP端口1434必须是可访问的。
	* `fallbackToDefaultDb` false, 使用options.database 无法访问数据库时，连接将会失败。为true时，将使用用户的默认数据库
	* `enableAnsiNullDefault` true 开启解析null
	* `cancelTimeout` 0
	* `packetSize` 4096
	* `useUTC` true
	* `abortTransactionOnError` ''
	* `localAddress` ''
	* `useColumnNames` false, 返回数组或简直对
	* `camelCaseColumns` false, 列名首字母大写
	* `columnNameReplacer` null, ( columnName, index, columnMetaData ) => {} 列名处理 return string
	* `debug` 开启调试模式
		* `packet` false, packet的细节
		* `data` false, packet数据的细节
		* `payload` false, packet的有效负荷
		* `token` false token 流
	* `isolationLevel`: 'READ_COMMITED', 事务运行的隔离级别  tds.ISOLATION_LEVEL ==> READ_UNCOMMITTED  READ_COMMITTED  REPEATABLE_READ SERIALIZABLE SNAPSHOT
	* `connectionIsolationLevel` 'READ_COMMITED', 新连接的隔离级别 同上
	* `readOnlyIntent`  false, 连接对数据库服务器是否只读权限
	* `encrypt`  false, 连接是否会被加密
	* `cryptoCredentialsDetails` {}, 当encrypt为真时， 该值作为 tls.createSecurePair 调用的第一个参数，默认是{}
	* `rowCollectionOnDone`  false, 是否在请求完成事件中接收所有行， 注意:如果接收到许多行，启用该选项可能导致内存占用过多。
	* `rowCollectionOnRequestCompletion` false 是否在请求的完成的回调中显示接收到的行

