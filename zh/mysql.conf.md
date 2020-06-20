# MySQL Parser Options instructions !
---

<br>
## Connection Options
* `host`: 连接到的数据库的主机名。. (Default: localhost)

* `port`: 要连接的端口号. (Default: 3306)

* `user`: 用于身份验证的MySQL用户.

*	`password`: 那个MySQL用户的密码.

*	`database`: 用于此连接的数据库名称(可选).

*	`localAddress`: 用于TCP连接的源IP地址. (Optional)

*	`socketPath`: 要连接到unix域套接字的路径。当使用主机和端口时被忽略.

*	`charset`: 连接的字符集。
	> 这在MySQL的sql级别(比如utf8_general_ci)中称为“collation”。如果指定了一个sql级别的charset(比如utf8mb4)，那么就会使用该字符集的默认排序。. (Default: 'UTF8_GENERAL_CI')
	>`提醒`: 这个值必须都是大写字母

*	`timezone`: 在MySQL服务器上配置的时区。这用于将cast服务器日期/时间值类型转换为JavaScript date对象，反之亦然。这可以是'local'， 'Z'，或者是形式的偏移量+HH:MM或-HH:MM. (Default: 'local')

*	`connectTimeout`: 在对MySQL服务器的初始连接期间发生超时之前的毫秒数. (Default: 10000)

*	`stringifyObjects`: 对象字符串化而不是转换为值。[请移步](https://github.com/mysqljs/mysql/issues/501). (Default: false)

*	`insecureAuth`: 允许连接到请求旧(不安全)身份验证方法的MySQL实例. (Default: false)

*	`typeCast`: 确定是否应该将列值转换为本地JavaScript类型. (Default: true)

*	`queryFormat`: 自定义查询格式函数。看到自定义格式.

*	`supportBigNumbers`: 在数据库中处理大数字(BIGINT和DECIMAL列)时，应该启用这个选项 (Default: false).

*	`bigNumberStrings`: 启用supportBigNumbers和bigNumberStrings可以强制将大数字(BIGINT和DECIMAL列)作为JavaScript字符串对象返回(默认为false)。启用supportBigNumbers但离开bigNumberStrings残疾将返回大数字字符串对象只有当他们不能准确地代表(JavaScript对象数量)(http://ecma262 - 5. - com/els5_html.htm # Section_8.5)(当他们超过[2 ^ 53 + 2 ^ 53]范围),否则他们将被作为数字对象返回。如果supportbignumber被禁用，此选项将被忽略.

*	`dateStrings`: 强制日期类型(TIMESTAMP, DATETIME, date)作为字符串返回，而不是膨胀到JavaScript对象。可以是真/假或一个类型名称的数组作为字符串?. (Default: false)

*	`debug`: 打印协议细节到stdout。可以是true/false或一组应该打印的包类型名称. (Default: false)

*	`trace`: 在错误上生成堆栈跟踪，包括库入口的调用位置(“长堆栈跟踪”)。对于大多数处理，轻微的性能损失。. (Default: true)

* `multipleStatements`: 允许每个查询使用多个mysql语句。要小心，它可能会增加SQL注入攻击的范围. (Default: false)

*	`flags`: 要使用的连接标志的列表，而不是默认的。还可以将默认值列入黑名单。有关更多信息，请检查连接标志.

*	`ssl`: [带有ssl参数的对象或包含ssl配置文件名称的字符串](https://github.com/mysqljs/mysql#ssl-options).

<br>
## ConnectionPool Options
* `acquireTimeout`: 在获取连接期间的超时毫秒数。这与connectTimeout略有不同，因为获取池连接并不总是涉及到连接. (Default: 10000)

* `waitForConnections`:当没有连接时，确定池的操作，并且达到了限制。如果是true，则池将对连接请求进行排队，并在可用时调用它。如果错误，池将立即返回错误. (Default: true)

* `connectionLimit`: 立即创建的最大连接数. (Default: 10)

* `queueLimit`: 在返回getConnection的错误之前，池将会排队的最大连接数。如果设置为0，队列连接请求的数量没有限制. (Default: 0)

<br>
## PoolCluster Options
*	`canRetry`: 如果是true，PoolCluster将尝试在连接失败时重新连接. (Default: true)

* `removeNodeErrorCount`: 如果连接失败，节点的errorCount增加。当errorCount大于removeNodeErrorCount时，删除PoolCluster中的一个节点。. (Default: 5)

* `restoreNodeTimeout`: 如果连接失败，开启另一个连接尝试之前的毫秒数。如果设置为0，则将删除节点，而不再重用该节点. (Default: 0)

* `defaultSelector`: 默认选择器. (Default: RR)

* `RR`: 轮询. (Round-Robin)

* `RANDOM`: 通过随机函数选择节点.

* `ORDER`: 无条件地选择第一个节点.
