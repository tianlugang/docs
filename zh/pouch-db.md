# PouchDB 中文文档

### 数据库创建

使用 `new PouchDB([name], [options])` 创建数据库，当数据库存在时，将 `连接并打开` 该数据库

* `name` 数据库名称，字符串类型，必要参数，当忽略名称只传入配置时，必须在配置中指明数据库名称。当使用 `indexDB` 作为 `adapter` 时， 数据库默认为 `name` 添加 `_pouch_ ` 前缀，不要手动在 `name` 上添加此前缀

* `options` 数据库配置，对象类型，
  + `options.name` 数据库名称。
  + `options.adapter` 数据库解析器。
    - 当 PouchDB 作为一个 `Nodejs` 客户端时， 配置中的其它选项将传给[request](https://github.com/mikeal/request)。
    - 在 `Nodejs` 端默认的 `adapter` 为 `leveldb` , `options` 中其他的选项将传给[levelup](https://github.com/rvagg/node-levelup)，[更多adapter的信息](https://pouchdb.com/adapters.html)

* 连接本地数据库时的 `options` 选项
  + `options.auto_compaction` : 布尔类型，默认值 false, 每当数据库产生变化后调用 `compact()` , 执行自动压缩.
  + `options.adapter` : 可选值 `idb` , `leveldb` , `http` , 默认为 `leveldb` 
  + `options.revs_limit` : 正整数类型，默认值 100，指定要跟踪的旧的修订（不是副本）的数量。当指定一个较低的数值时，意味着 PouchDB 可能无法确定是否通过复制接受的一个新的修订与 PouchDB 当前存在的可能导致冲突的修订有关
  + `options.deterministic_revs` : 布尔类型，默认值true，使用 `md5 hash` 为文档创建确定的修订编号。当设置为 false, 将意味着这个新的修订编号是一个随机的 `UUID` 

* 连接远程数据库时 `options` 选项
  + `options.fetch(url, opts)` : 拦截或覆盖由 PouchDB 向远程数据库发出的 `HTTP` 连接请求，可以在此添加或修改此请求的 `首部（headers）` 或 `配置（options）` , 本请求返回一个 `延迟（Promise）` 对象
  + `options.auth.username` + `options.auth.password` : 指定向远程数据库发出请求时的 `HTTP` 连接请求所需的权限校验参数 `用户名（username）` 和 `密码（password）` ，还可以通过如此 `http://user:pass@host/name` 方式代替此选项
  + `options.skip_setup` : 布尔值，默认值 false，初始化数据库连接时指定 PouchDB 是否检测该数据库已经存在, 并且当它不存在时就尝试创建它，此选项设置为 true 时将跳过反之的操作

``` js
    var PouchDB = require('pouchdb');
    var db = new PouchDB('mydata');

    // 必须安装 pouchdb-adapter-node-websql
    var db = new PouchDB({
        name: 'mydata.db',
        adapter: 'websql'
    });

    // 必须安装 pouchdb-adapter-memory
    var db = new PouchDB('mydata.db', {
        adapter: 'memory'
    });

    // 连接本地服务端
    var db = new PouchDB('http://localhost:5984/dbname');

    // 连接指定域的服务端
    var db = new PouchDB('http://example.com/dbname', {
        fetch: function(url, opts) {
            opts.headers.set('X-Some-Special-Header', 'foo');
            return PouchDB.fetch(url, opts);
        }
    });
```

* 重复使用相同的构造函数的配置时，可以用 `PouchDB.defaults()` 来简化代码，它将返回一个与 PouchDB 功能相同的构造函数， 支持所有的 PouchDB 配置，且可以单独覆写配置

``` js
    var MyMemPouch = PouchDB.defaults({
        adapter: 'memory'
    });
    // In-memory PouchDB
    var myMemPouch = new MyMemPouch('dbname');

    var MyPrefixedPouch = PouchDB.defaults({
        prefix: '/path/to/my/db/'
    });
    // db will be named '/path/to/my/db/dbname', useful for LevelDB
    var myPrefixedPouch = new MyPrefixedPouch('dbname');

    var HTTPPouch = PouchDB.defaults({
        prefix: 'http://example.org'
    });

    // db will be located at 'http://example.org/dbname'
    var myHttpPouch = new HTTPPouch('dbname');
```

### 数据库删除

`db.destroy([options], [callback])` 参数可选，无参时返回 `Promise` , 本操作对其他复制的数据库没有影响

``` js
    try {
        await db.destroy();
    } catch (err) {
        console.log(err);
    }
```

### 数据库关闭

`db.close([callback])` 关闭数据库，这将关闭到底层存储的任何打开连接，并释放数据库可能正在使用的内存(事件监听器)。

``` js
    await db.close();
```

### 数据库查看

`db.info([callback])` 查看当前数据库的信息

``` js
    try {
        var result = await db.info();
    } catch (err) {
        console.log(err);
    }

    var result = {
        "db_name": "test",
        "doc_count": 4,
        "update_seq": 5
    }
```

* 结果集分析
  + `db_name` 数据库名称，是在执行 `new PouchDB()` 时给出的 `name` , 同时也是数据库的唯一标识
  + `doc_count` 数据库未被删除的文档总数
  + `update_seq` 数据库的序列号，它从0开始，并在每次添加或修改文档时递增

  **注意**
  下面的属性可用于开发时调试，它们都是非官方的，并且随时可能发生变化

  + `adapter` 数据库使用的 `adapter` 名称(idb, leveldb, …)
  + `idb_attachment_format` 数据库附件数据格式，其中IndexedDB用的'base64'或'binary', 取决于浏览器是否支持二进制blob
  + `backend_adapter` 后端使用的 `adapter` , Node. JS后端可能使用MemDOWN, RiakDOWN等

### 数据库压缩

`db.compact([options], [callback])` 在本地或远程数据库上触发压缩操作，删除没用的老旧数据，即那些修订不再引用的非叶修订和附件，以减少数据库大小。对于远程数据库， PouchDB 将定期检测 `compaction status` 并在回调 `callback` (或promise的resolves)执行完成后，详情请查看[CouchDB维护文档的压缩部分](http://couchdb.readthedocs.org/en/latest/maintenance/compaction.html)。而对于本地数据库将自动执行压缩操作，[请看](https://pouchdb.com/api.html#create_database)

* `options` 配置项
  + `options.interval` 在再次询问是否已完成压缩之前等待的毫秒数。默认为200。(仅适用于远程数据库。)

``` js
    try {
        var result = await db.compact();
    } catch (err) {
        console.log(err);
    }

    var result = {
        "ok": "true"
    }
```

### 数据库监听

`db.changes(options)` 对数据库中的文档所做更改的列表，按顺序排列，返回带有cancel()方法的事件对象，在每个文档产生变化时触发 `change` 事件，在文档变化被成功处理之后相应 `complete` 事件，当发生错误时触发 `error` 事件 调用 `cancel` 将自动退订所有的事件监听器

* `options` 配置项, 默认均为 false 除非另有说明
  + `options.live` : 为将发生的所有变化都触发 `change` 事件，直到调用了 `cancel` 
  + `options.include_docs` : 每次文档变化时都传出相关的文档
  + `options.conflicts` :  每次文档变化时都传出相关的文档冲突
  + `options.attachments` :  每次文档变化时都传出相关附件数据
  + `options.binary` : 使用 `Blobs/Buffers` 代替 `base64-encoded` 格式返回附件数据
  + `options.descending` : 降序排列文档集
  + `options.since` : 在给定序列号之后立即从更改中给出结果，当 `options.live` 为 `true` 时，如果只想要新的变化集，可以将此项设为 `now` 
  + `options.limit` : 将结果的数量限制为这个数量
  + `options.timeout` : 请求超时的毫秒时, 设为 `false` 将禁用此项
  + `options.heartbeat` : 只针对 `http adapter` 有效, 服务器给出一个心跳以保持长连接打开的时间(以毫秒为单位)，默认值 100000 (10秒), 设置为 `false` 将禁用此默认行为  

* **`Filtering Options`** 帅选项配置
  + `options.filter` : 从设计文档中引用筛选器函数以有选择地获取更新。使用视图函数时，可传入 `_pass` , 此时必须 `options.new` 为一个函数，[详情](#filtered-changes)
  + `options.doc_ids` : `_id` 数组，变化的文档集中只包含数组指定ID的项
  + `options.query_params` : 对象，其中包含传递给筛选器函数的属性, 例如： `{"foo:"bar"}` , 筛选函数： `function (doc, params) {/* ... */}` ， `"bar"` 可在此函数中使用( `params.query.foo` )
  + `options.view` : 指定一个视图函数作为过滤器(例如： `'design_doc_name/view_name'` 或 `'view_name/view_name'` （可简称 `'view_name'` ）)。如果一个map函数为视图过滤器发出至少一条记录，那么这些文档就被认为是“传递的”，而 `options.filter` 必须设为 `'_view'` 这个选项才可以运行
  + `options.selector` : 过滤查询的条件，[详情](http://docs.couchdb.org/en/2.0.0/api/database/find.html#selector-syntax) `CouchDB 1.x` 不支持选择器，不能与 `options.filter` 组合使用

* **Advanced Options:** 高级配置项
  + `options.return_docs` : 默认为 `true` 除了当 `options.live = true` 默认为 `false` . Passing `false` prevents the changes feed from keeping all the documents in memory &ndash; in other words complete always has an empty results array, and the `change` event is the only way to get the event. Useful for large change sets where otherwise you would run out of memory.
  + `options.batch_size` : Only available for http databases, this configures how many changes to fetch at a time. Increasing this can reduce the number of requests made. Default is 25.
  + `options.style` : Specifies how many revisions are returned in the changes array. The default, `'main_only'` , will only return the current "winning" revision; `'all_docs'` will return all leaf revisions (including conflicts and deleted former conflicts). Most likely you won't need this unless you're writing a replicator.
  + `options.seq_interval` : Only available for http databases. Specifies that seq information only be generated every N changes. Larger values can improve changes throughput with CouchDB 2.0 and later. Note that `last_seq` is always populated regardless.

``` js
var changes = db.changes({
    since: 'now',
    live: true,
    include_docs: true
}).on('change', function(change) {
    {
        "id": "somestuff",
        "seq": 21,
        "changes": [{

            "rev": "1-8e6e4c0beac3ec54b27d1df75c7183a8"

        }],
        "doc": {

            "title": "Ch-Ch-Ch-Ch-Changes",
            "_id": "someDocId",
            "_rev": "1-8e6e4c0beac3ec54b27d1df75c7183a8"

        }
    }
    // handle change
}).on('complete', function(info) {
    // changes() was canceled
}).on('error', function(err) {
    console.log(err);
});
// whenever you want to cancel
changes.cancel();
```

#### Change events

* __ `change` __ ( `info` ) - This event fires when a change has been found. `info` will contain details about the change, such as whether it was deleted and what the new `_rev` is. `info.doc` will contain the doc if you set `include_docs` to `true` . See below for an example response.
* __ `complete` __ ( `info` ) - This event fires when all changes have been read. In live changes, only cancelling the changes should trigger this event. `info.results` will contain the list of changes. See below for an example.
* __ `error` __ ( `err` ) - This event is fired when the changes feed is stopped due to an unrecoverable failure.

#### Example response

Example response in the `'change'` listener (using `{include_docs: true}` ):

{% highlight js %}
{ id: 'doc1', 
  changes: [ { rev: '1-9152679630cc461b9477792d93b83eae' } ], 
  doc: {

    _id: 'doc1',
    _rev: '1-9152679630cc461b9477792d93b83eae'

  }, 
  seq: 1
}
{% endhighlight %}

Example response in the `'change'` listener when a doc was deleted:

{% highlight js %}
{ id: 'doc2', 
  changes: [ { rev: '2-9b50a4b63008378e8d0718a9ad05c7af' } ], 
  doc: { _id: 'doc2', 

    _rev: '2-9b50a4b63008378e8d0718a9ad05c7af',
    _deleted: true

  }, 
  deleted: true, 
  seq: 3
}
{% endhighlight %}

Example response in the `'complete'` listener:

{% highlight js %}
{
  "results": [

    {
      "id": "doc1", 
      "changes": [ { "rev": "1-9152679630cc461b9477792d93b83eae" } ], 
      "doc": {
        "_id": "doc1", 
        "_rev": "1-9152679630cc461b9477792d93b83eae"
      }, 
      "seq": 1
    }, 
    {
      "id": "doc2", 
      "changes": [ { "rev": "2-9b50a4b63008378e8d0718a9ad05c7af" } ], 
      "doc": {
        "_id": "doc2", 
        "_rev": "2-9b50a4b63008378e8d0718a9ad05c7af", 
        "_deleted": true
      }, 
      "deleted": true, 
      "seq": 3
    }

  ], 
  "last_seq": 3
}
{% endhighlight %}

`seq` and `last_seq` correspond to the overall sequence number of the entire database, and it's what is passed in when using `since` (except for the special `'now'` ). It is the primary key for the changes feed, and is also used as a checkpointer by the replication algorithm.

#### Single-shot

If you don't specify `{live: true}` , then you can also use `changes()` in the standard
callback/promise style, and it will be treated as a single-shot request, which
returns a list of the changes (i.e.what the `'complete'` event emits):

{% include code/start.html id="changes1" type="callback" %}
{% highlight js %}
db.changes({
  limit: 10, 
  since: 0
}, function (err, response) {
  if (err) { return console.log(err); }
  // handle result
}); 
{% endhighlight %}
{% include code/end.html %}

{% include code/start.html id="changes1" type="async" %}
{% highlight js %}
try {
  var result = await db.changes({

    limit: 10,
    since: 0

  }); 
} catch (err) {
  console.log(err); 
}
{% endhighlight %}
{% include code/end.html %}

{% include code/start.html id="changes1" type="promise" %}
{% highlight js %}
db.changes({
  limit: 10, 
  since: 0
}).then(function (result) {
  // handle result
}).catch(function (err) {
  console.log(err); 
}); 
{% endhighlight %}
{% include code/end.html %}

#### Example Response:

{% highlight js %}
{
  "results": [{

    "id": "0B3358C1-BA4B-4186-8795-9024203EB7DD",
    "seq": 1,
    "changes": [{
      "rev": "1-5782E71F1E4BF698FA3793D9D5A96393"
    }]

  }, {

    "id": "mydoc",
    "seq": 2,
    "changes": [{
      "rev": "1-A6157A5EA545C99B00FF904EEF05FD9F"
    }]

  }, {

    "id": "otherdoc",
    "seq": 3,
    "changes": [{
      "rev": "1-3753476B70A49EA4D8C9039E7B04254C"
    }]

  }, {

    "id": "828124B9-3973-4AF3-9DFD-A94CE4544005",
    "seq": 4,
    "changes": [{
      "rev": "1-A8BC08745E62E58830CA066D99E5F457"
    }]

  }], 
  "last_seq": 4
}
{% endhighlight %}

When `live` is `false` , the returned object is also an event emitter as well as a promise, 
and will fire the `'complete'` event when the results are ready.

Note that this `'complete'` event only fires when you aren't doing live changes.

#### Filtered changes

As with [replicate()](#replication), you can filter using:

* an ad-hoc `filter` function
* an array of `doc_ids` 
* a `filter` function inside of a design document
* a `filter` function inside of a design document, with `query_params` 
* a `view` function inside of a design document

If you are running `changes()` on a remote CouchDB, then the first method will run client-side, whereas the last four will filter on the server side. Therefore the last four should be preferred, especially if the database is large, because you want to send as few documents over the wire as possible.

If you are running `changes()` on a local PouchDB, then obviously all five methods will run client-side. There are also no performance benefits to using any of the five, so can also just filter yourself, in your own `on('change')` handler. These methods are implemented in PouchDB purely for consistency with CouchDB.

The named functions can either be specified with `'designdoc_id/function_name'` or (if both design doc id and function name are equal) as `'fname'` as shorthand for `'fname/fname'` .

#### Filtering examples

In these examples, we'll work with some mammals. Let's imagine our docs are:

{% highlight js %}
[
  {_id: 'a', name: 'Kangaroo', type: 'marsupial'}, 
  {_id: 'b', name: 'Koala', type: 'marsupial'}, 
  {_id: 'c', name: 'Platypus', type: 'monotreme'}
]
{% endhighlight %}

Here are 5 examples using the 5 different systems.

**Example 1: Ad-hoc `filter` function**

*Warning*: this runs client-side, if the database is remote.

Filter by `type === 'marsupial'` :

{% highlight js %}
db.changes({
  filter: function (doc) {

    return doc.type === 'marsupial';

  }
}); 
{% endhighlight %}

**Example 2: Array of `doc_ids` **

Filter documents with `_id` s `['a', 'c']` .

{% highlight js %}
db.changes({
  doc_ids: ['a', 'c']
}); 
{% endhighlight %}

**Example 3: `filter` function inside of a design document**

First `put()` a design document:

{% highlight js %}
{
  _id: '_design/mydesign', 
  filters: {

    myfilter: function (doc) {
      return doc.type === 'marsupial';
    }.toString()

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
db.changes({
  filter: 'mydesign/myfilter'
}); 
{% endhighlight %}

**Example 4: `filter` function inside of a design document, with `query_params` **

This is the most powerful way to filter, because it allows you to pass in arbitrary options to your filter function.

First `put()` a design document:

{% highlight js %}
{
  _id: '_design/myfilter', 
  filters: {

    myfilter: function (doc, req) {
      return doc.type === req.query.type;
    }.toString()

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
db.changes({
  filter: 'myfilter', 
  query_params: {type: 'marsupial'}
}); 
{% endhighlight %}

Since both the design document and the filter function have the same name, we can shorten the function name to `'myfilter'` .

**Example 5: `view` function inside of a design document**

This doesn't really offer any advantages compared to the previous two methods, unless you are already using a `view` for map/reduce queries, and you want to reuse it.

Any documents that `emit()` anything will be considered to have passed this filter method.

First `put()` a design document:

{% highlight js %}
{
  _id: '_design/mydesign', 
  views: {

    myview: function (doc) {
      if (doc.type === 'marsupial') {
        emit(doc._id);
      }
    }.toString()

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
db.changes({
  filter: '_view', 
  view: 'mydesign/myview'
}); 
{% endhighlight %}

### 数据库复制

* `PouchDB.replicate(source, target, [options])` , 将数据从 `source` 复制到 `target` ，该方法返回一个包含 `cancel()` 方法的对象， `cancel` 方法可用于取消终止复制操作
* `source` 和 `target` 可以是 PouchDB 的实例、一个指向 `CouchDB` 数据库的URL或本地数据库的名称。

{% include anchor.html edit="true" title="Replicate a database" hash="replication" %}

{% highlight js %}
PouchDB.replicate(source, target, [options])
{% endhighlight %}

Replicate data from `source` to `target` . Both the `source` and `target` can be a PouchDB instance or a string representing a CouchDB database URL or the name of a local PouchDB database. If `options.live` is `true` , then this will track future changes and also replicate them automatically. This method returns an object with the method `cancel()` , which you call if you want to cancel live replication.

Replication is an [event emitter][] like [changes()](#changes) and emits the `'complete'` , `'active'` , `'paused'` , `'change'` , `'denied'` and `'error'` events.

### Options

All options default to `false` unless otherwise specified.

* `options.live` : If `true` , starts subscribing to future changes in the `source` database and continue replicating them.
* `options.retry` : If `true` will attempt to retry replications in the case of failure (due to being offline), using a backoff algorithm that retries at longer and longer intervals until a connection is re-established, with a maximum delay of 10 minutes. Only applicable if `options.live` is also `true` .

**Filtering Options:**

* `options.filter` : Reference a filter function from a design document to selectively get updates. To use a view function, pass `_view` here and provide a reference to the view function in `options.view` . See [filtered replication](#filtered-replication) for details.
* `options.doc_ids` : Only show changes for docs with these ids (array of strings).
* `options.query_params` : Object containing properties that are passed to the filter function, e.g. `{"foo":"bar"}` , where `"bar"` will be available in the filter function as `params.query.foo` . To access the `params` , define your filter function like `function (doc, params) {/* ... */}` .
* `options.view` : Specify a view function (e.g. `'design_doc_name/view_name'` or `'view_name'` as shorthand for `'view_name/view_name'` ) to act as a filter. Documents counted as "passed" for a view filter if a map function emits at least one record for them.**Note**: `options.filter` must be set to `'_view'` for this option to work.
* `options.selector` : Filter using a query/pouchdb-find [selector](http://docs.couchdb.org/en/2.0.0/api/database/find.html#selector-syntax).**Note**: Selectors are not supported in CouchDB 1.x.

**Advanced Options:**

* `options.since` : Replicate changes after the given sequence number.
* `options.heartbeat` : Configure the heartbeat supported by CouchDB which keeps the change connection alive.
* `options.timeout` : Request timeout (in milliseconds).
* `options.batch_size` : Number of change feed items to process at a time. Defaults to 100. This affects the number of docs and attachments held in memory and the number sent at a time to the target server. You may need to adjust downward if targeting devices with low amounts of memory (e.g.phones) or if the documents and/or attachments are large in size or if there are many conflicted revisions. If your documents are small in size, then increasing this number will probably speed replication up.
* `options.batches_limit` : Number of batches to process at a time. Defaults to 10. This (along wtih `batch_size` ) controls how many docs are kept in memory at a time, so the maximum docs in memory at once would equal `batch_size` &times; `batches_limit` .
* `options.back_off_function` : backoff function to be used in `retry` replication. This is a function that takes the current backoff as input (or 0 the first time) and returns a new backoff in milliseconds. You can use this to tweak when and how replication will try to reconnect to a remote database when the user goes offline. Defaults to a function that chooses a random backoff between 0 and 2 seconds and doubles every time it fails to connect. The default delay will never exceed 10 minutes.(See [Customizing retry replication](#customizing-retry-replication) below.)
* `options.checkpoint` : Can be used if you want to disable checkpoints on the source, target, or both. Setting this option to `false` will prevent writing checkpoints on both source and target. Setting it to `source` will only write checkpoints on the source. Setting it to `target` will only write checkpoints on the target.

#### Example Usage:

{% highlight js %}
var rep = PouchDB.replicate('mydb', 'http://localhost:5984/mydb', {
  live: true, 
  retry: true
}).on('change', function (info) {
  // handle change
}).on('paused', function (err) {
  // replication paused (e.g.replication up to date, user went offline)
}).on('active', function () {
  // replicate resumed (e.g.new changes replicating, user went back online)
}).on('denied', function (err) {
  // a document failed to replicate (e.g.due to permissions)
}).on('complete', function (info) {
  // handle complete
}).on('error', function (err) {
  // handle error
}); 

rep.cancel(); // whenever you want to cancel
{% endhighlight %}

There are also shorthands for replication given existing PouchDB objects. These behave the same as `PouchDB.replicate()` :

{% highlight js %}
db.replicate.to(remoteDB, [options]); 
// or
db.replicate.from(remoteDB, [options]); 
{% endhighlight %}

The `remoteDB` can either be a string or a `PouchDB` object. If you have a fetch override on a remote database, you will want to use `PouchDB` objects instead of strings, so that the options are used.

#### Replication events

* __ `change` __ ( `info` ) - This event fires when the replication has written a new document. `info` will contain details about the change. `info.docs` will contain the docs involved in that change. See below for an example response.
* __ `complete` __ ( `info` ) - This event fires when replication is completed or cancelled. In a live replication, only cancelling the replication should trigger this event. `info` will contain details about the replication. See below for an example response.
* __ `paused` __ ( `err` ) - This event fires when the replication is paused, either because a live replication is waiting for changes, or replication has temporarily failed, with `err` , and is attempting to resume.
* __ `active` __ - This event fires when the replication starts actively processing changes; e.g.when it recovers from an error or new changes are available.
* __ `denied` __ ( `err` ) - This event fires if a document failed to replicate due to validation or authorization errors.
* __ `error` __ ( `err` ) - This event is fired when the replication is stopped due to an unrecoverable failure. If `retry` is `false` , this will also fire when the user goes offline or another network error occurs (so you can handle retries yourself, if you want).

#### Single-shot

As with [changes()](#changes), you can also omit `live` , in which case you can use `replicate()` in the callback/promise style and it will be treated as a single-shot operation.

{% include code/start.html id="replication1" type="callback" %}
{% highlight js %}
db.replicate.to(remote, function (err, result) {
  if (err) { return console.log(err); }
  // handle 'completed' result
}); 
{% endhighlight %}
{% include code/end.html %}

{% include code/start.html id="replication1" type="async" %}
{% highlight js %}
try {
  var result = await db.replicate.to(remote); 
} catch (err) {
  console.log(err); 
}
{% endhighlight %}
{% include code/end.html %}

{% include code/start.html id="replication1" type="promise" %}
{% highlight js %}
db.replicate.to(remote).then(function (result) {
  // handle 'completed' result
}).catch(function (err) {
  console.log(err); 
}); 
{% endhighlight %}
{% include code/end.html %}

For non-live replications, the returned object is also an event emitter as well as a promise, and you can use all the events described above (except for `'paused'` and `'active'` , which only apply to `retry` replications).

#### Example Response:

Example response in the `'change'` listener:

{% highlight js %}
{
  "doc_write_failures": 0, 
  "docs_read": 1, 
  "docs_written": 1, 
  "errors": [], 
  "last_seq": 1, 
  "ok": true, 
  "start_time": "Fri May 16 2014 18:23:12 GMT-0700 (PDT)", 
  "docs": [

    { _id: 'docId',
      _rev: '1-e798a1a7eb4247b399dbfec84ca699d4',
      and: 'data' }

  ]
}
{% endhighlight %}

Example response in the `'complete'` listener:

{% highlight js %}
{
  "doc_write_failures": 0, 
  "docs_read": 2, 
  "docs_written": 2, 
  "end_time": "Fri May 16 2014 18:26:00 GMT-0700 (PDT)", 
  "errors": [], 
  "last_seq": 2, 
  "ok": true, 
  "start_time": "Fri May 16 2014 18:26:00 GMT-0700 (PDT)", 
  "status": "complete"
}
{% endhighlight %}

Note that replication is supported for both local and remote databases. So you can replicate from local to local or from remote to remote.

However, if you replicate from remote to remote, then the changes will flow through PouchDB. If you want to trigger a server-initiated replication, please use regular ajax to POST to the CouchDB `_replicate` endpoint, as described [in the CouchDB docs](https://docs.couchdb.org/en/stable/api/server/common.html#api-server-replicate).

#### Filtered replication

As with [changes()](#changes), you can filter from the source database using:

* an ad-hoc `filter` function
* an array of `doc_ids` 
* a `filter` function inside of a design document
* a `filter` function inside of a design document, with `query_params` 
* a `view` function inside of a design document

If you are replicating from a remote CouchDB, then the first method will run client-side, whereas the last four will filter on the server side. Therefore the last four should be preferred, especially if the database is large, because you want to send as few documents over the wire as possible.

You should also beware trying to use filtered replication to enforce security, e.g.to partition a database per user. A better strategy is the ["one database per user" method](https://github.com/nolanlawson/pouchdb-authentication#couchdb-authentication-recipes).

{% include alert/start.html variant="warning" %}

{% markdown %}

**Deleting filtered docs**: When you use filtered replication, you should avoid using `remove()` to delete documents, because that removes all their fields as well, which means they might not pass the filter function anymore, causing the deleted revision to not be replicated. Instead, set the `doc._deleted` flag to `true` and then use `put()` or `bulkDocs()` .

{% endmarkdown %}

{% include alert/end.html %}

#### Filtering examples

In these examples, we'll work with some mammals. Let's imagine our docs are:

{% highlight js %}
[
  {_id: 'a', name: 'Kangaroo', type: 'marsupial'}, 
  {_id: 'b', name: 'Koala', type: 'marsupial'}, 
  {_id: 'c', name: 'Platypus', type: 'monotreme'}
]
{% endhighlight %}

Here are 5 examples using the 5 different systems.

**Example 1: Ad-hoc `filter` function**

*Warning*: this runs client-side, if you are replicating from a remote database.

Filter by `type === 'marsupial'` :

{% highlight js %}
remote.replicate.to(local, {
  filter: function (doc) {

    return doc.type === 'marsupial';

  }
}); 
{% endhighlight %}

**Example 2: Array of `doc_ids` **

Filter documents with `_id` s `['a', 'c']` .

{% highlight js %}
remote.replicate.to(local, {
  doc_ids: ['a', 'c']
}); 
{% endhighlight %}

**Example 3: `filter` function inside of a design document**

First `put()` a design document in the remote database:

{% highlight js %}
{
  _id: '_design/mydesign', 
  filters: {

    myfilter: function (doc) {
      return doc.type === 'marsupial';
    }.toString()

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
remote.replicate.to(local, {
  filter: 'mydesign/myfilter'
}); 
{% endhighlight %}

**Example 4: `filter` function inside of a design document, with `query_params` **

This is the most powerful way to filter, because it allows you to pass in arbitrary options to your filter function.

First `put()` a design document in the remote database:

{% highlight js %}
{
  _id: '_design/mydesign', 
  filters: {

    myfilter: function (doc, req) {
      return doc.type === req.query.type;
    }.toString()

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
remote.replicate.to(local, {
  filter: 'mydesign/myfilter', 
  query_params: {type: 'marsupial'}
}); 
{% endhighlight %}

**Example 5: `view` function inside of a design document**

This doesn't really offer any advantages compared to the previous two methods, unless you are already using a `view` for map/reduce queries, and you want to reuse it.

Any documents that `emit()` anything will be considered to have passed this filter method.

First `put()` a design document in the remote database:

{% highlight js %}
{
  _id: '_design/mydesign', 
  views: {

    myview: {
      map: function(doc) {
        if (doc.type === 'marsupial') {
          emit(doc._id); 
        }
      }.toString()
    }

  }
}
{% endhighlight %}

Then filter by `type === 'marsupial'` :

{% highlight js %}
remote.replicate.to(local, {
  filter: '_view', 
  view: 'mydesign/myview'
}); 
{% endhighlight %}

#### Customizing retry replication

During `retry` replication, you can customize the backoff function that determines how long to wait before reconnecting when the user goes offline.

Here's a simple backoff function that starts at 1000 milliseconds and triples it every time a remote request fails:

{% highlight js %}

db.replicate.to(remote, {
  live: true, 
  retry: true, 
  back_off_function: function (delay) {

    if (delay === 0) {
      return 1000; 
    }
    return delay * 3; 

  }
}); 

{% endhighlight %}

The first time a request fails, this function will receive 0 as input. The next time it fails, 1000 will be passed in, then 3000, then 9000, etc. When the user comes back online, the `delay` goes back to 0.

By default, PouchDB uses a backoff function that chooses a random starting number between 0 and 2000 milliseconds and will roughly double every time, with some randomness to prevent client requests from occurring simultaneously.

### 数据库同步

{% include anchor.html edit="true" title="Sync a database" hash="sync" %} 

{% highlight js %}
var sync = PouchDB.sync(src, target, [options])
{% endhighlight %}

Sync data from `src` to `target` and `target` to `src` . This is a convenience method for bidirectional data replication.

In other words, this code:

{% highlight js %}
PouchDB.replicate('mydb', 'http://localhost:5984/mydb'); 
PouchDB.replicate('http://localhost:5984/mydb', 'mydb'); 
{% endhighlight %}

is equivalent to this code:

{% highlight js %}
PouchDB.sync('mydb', 'http://localhost:5984/mydb'); 
{% endhighlight %}

### Options

* `options.push` + `options.pull` : Allows you to specify separate [replication options](api.html#replication) for the individual replications.

Replication options such as `filter` passed to sync directly will be passed to both replications. Please refer to [replicate()](api.html#replication) for documentation on those options.

#### Example Usage:

{% highlight js %}
var sync = PouchDB.sync('mydb', 'http://localhost:5984/mydb', {
  live: true, 
  retry: true
}).on('change', function (info) {
  // handle change
}).on('paused', function (err) {
  // replication paused (e.g.replication up to date, user went offline)
}).on('active', function () {
  // replicate resumed (e.g.new changes replicating, user went back online)
}).on('denied', function (err) {
  // a document failed to replicate (e.g.due to permissions)
}).on('complete', function (info) {
  // handle complete
}).on('error', function (err) {
  // handle error
}); 

sync.cancel(); // whenever you want to cancel
{% endhighlight %}

There is also a shorthand for syncing given existing PouchDB objects. This behaves the same as `PouchDB.sync()` :

{% highlight js %}
db.sync(remoteDB, [options]); 
{% endhighlight %}

It is also possible to combine "one-way" replication and sync for performance reasons.
When your PouchDB application starts up it could perform a one-off, one-way replication to completion and then initiate the two-way, continuous retryable sync:

{% highlight js %}
var url = 'http://localhost:5984/mydb'; 
var opts = { live: true, retry: true }; 

// do one way, one-off sync from the server until completion
db.replicate.from(url).on('complete', function(info) {
  // then two-way, continuous, retriable sync
  db.sync(url, opts)

    .on('change', onSyncChange)
    .on('paused', onSyncPaused)
    .on('error', onSyncError);

}).on('error', onSyncError); 
{% endhighlight %}

The above technique results in fewer HTTP requests being used and better performance than just using `db.sync` on its own.

#### Example Response:

Change events in `sync` have an extra property `direction` which refers to the direction the change was going. Its value will either be `push` or `pull` .

{% highlight js %}
{ direction: 'push', 
  change:
   { ok: true, 

     start_time: '2015-10-21T15:26:51.151Z',
     docs_read: 1,
     docs_written: 1,
     doc_write_failures: 0,
     errors: [],
     last_seq: 1,
     docs: [ [Object] ] } }

{% endhighlight %}

For any further details, please refer to [replicate()](api.html#replication).

### 更新文档

* `db.put(doc, [options], [callback])` , 参数 `doc` 必须时一个纯粹的 `JSON` 对象，一个键值对集合，如果尝试存储非 `JSON` 数据，[将会产生一些不一致的结果](http://pouchdb.com/errors.html#could_not_be_cloned)

* 当文档存在时你必须指定它的修订号 `_rev` , 否则就会产生冲突，如果需要更新现有文档，即便存在冲突，可以指定当前文档的 `_rev` 并且配合 `options.force = true` ，这将会创建一个新的文档冲突

``` js
    // 创建文档
    try {
        var response = await db.put({
            _id: 'mydoc',
            title: 'Heroes'
        });
    } catch (err) {
        console.log(err);
    }

    // 更新文档
    try {
        var doc = await db.get('mydoc');
        var response = await db.put({
            _id: 'mydoc',
            _rev: doc._rev,
            title: "Let's Dance"
        });
    } catch (err) {
        console.log(err);
    }

    // response 举例
    // id 为当前文档的id，rev为新建的，ok表示该操作成功执行
    var response = {
        "ok": true,
        "id": "mydoc",
        "rev": "1-A6157A5EA545C99B00FF904EEF05FD9F"
    };
```

### 创建文档

* `db.post(doc, [options], [callback])` 
* 该方法无需指定文档id， PouchDB 会为该文档自动生成 `_id` 属性, 而 `db.put` 则必须指定 `_id` 
* 推荐使用 `db.put` 新建文档，[请看](https://pouchdb.com/2014/06/17/12-pro-tips-for-better-code-with-pouchdb.html), `db.post` 生成 `_id` 时存在效率问题，并且ID时随机的，不便于文档排序

``` js
    try {
        var response = await db.post({
            title: 'Ziggy Stardust'
        });
    } catch (err) {
        console.log(err);
    }

    var response = {
        "ok": true,
        "id": "8A2C3761-FFD5-4770-9B8C-38C33CED300A",
        "rev": "1-d3a8e0e5aa7c8fff0c376dac2d8a4007"
    }
```

### 获取文档

* `db.get(docId, [options], [callback])` , `docId` 为必要参数， `options` 的所有选项均为false，除非手动开启
* 配置项说明
  + `options.rev` : 获取文档的特定修订，默认获取最终的 `rev` 
  + `options.revs` : 是否包括文档的修订历史
  + `options.revs_info` : 是否包括文档的修订列表及其可用性
  + `options.open_revs` : 当设置为 `all` 时获取所有的叶子修订，当指定为一个数字时获取数组时，将按照输入数组中指定的顺序返回
  + `options.conflicts` : 如果指定为true，冲突的叶子修订将附件到 `_conflicts` 数组
  + `options.attachments` : 是否包含附件数据
  + `options.binary` : 是否以 `Blobs/Buffers` 代替 `base64-encoded` 返回附件数据
  + `options.latest` : 强制获取最新的 “leaf” 修订, 无论 `rev` 是否被请求，默认时false

``` js
    try {
        var doc = await db.get('mydoc');
    } catch (err) {
        console.log(err);
    }

    var doc = {
        "_id": "mydoc",
        "_rev": "1-A6157A5EA545C99B00FF904EEF05FD9F"
        "title": "Rock and Roll Heart",
    }
```

### 删除文档

* `db.remove(doc, [options], [callback])` 或 `db.remove(docId, docRev, [options], [callback])` 
* `doc` 为必要参数，且至少包含 `_id` 与 `_rev` 属性
* 使用 `db.put()` 与配置 `{_deleted: true}` 可实现假删

``` js
    try {
        var doc = await db.get('mydoc');
        var response = await db.remove(doc);
    } catch (err) {
        console.log(err);
    }

    // 或者
    try {
        var doc = await db.get('mydoc');
        var response = await db.remove(doc._id, doc._rev);
    } catch (err) {
        console.log(err);
    }

    // 返回值
    var response = {
        "ok": true,
        "id": "mydoc",
        "rev": "2-9AF304BE281790604D1D8A4B0F4C9ADB"
    }

    // 假删
    try {
        var doc = await db.get('mydoc');
        doc._deleted = true;
        var result = await db.put(doc);
    } catch (err) {
        console.log(err);
    }
```

### 批量新增/更新/删除文档

* `db.bulkDocs(docs, [options], [callback])` 参数 `docs` 是一个文档集合（数组），且必须提供

* 如果忽略文档集中某一成员的 `_id` 属性, 数据库将创建新的文档合并到传入的集合里。而更新一个成员，必须指定该成员的 `_id` 和 `_rev` 属性, 它们应该与相应文档的 `_id` 和 `_rev` 相匹配，并将其作为更新的基础。最后，删除一个成员时, 该成员需包含 `_delete` ，且为true

``` js
    // Put 一些新的文档，提供 _id
    try {
        var result = await db.bulkDocs([{
                title: 'Lisa Says',
                _id: 'doc1'
            },
            {
                title: 'Space Oddity',
                _id: 'doc2'
            }
        ]);
    } catch (err) {
        console.log(err);
    }

    // Post 一些新的文档，自动生成 _id
    try {
        var result = await db.bulkDocs([{
                title: 'Lisa Says'
            },
            {
                title: 'Space Oddity'
            }
        ]);
    } catch (err) {
        console.log(err);
    }

    // 更新
    try {
        var result = await db.bulkDocs([{
                title: 'Lisa Says',
                artist: 'Velvet Underground',
                _id: "doc1",
                _rev: "1-84abc2a942007bee7cf55007cba56198"
            },
            {
                title: 'Space Oddity',
                artist: 'David Bowie',
                _id: "doc2",
                _rev: "1-7b80fc50b6af7a905f368670429a757e"
            }
        ]);
    } catch (err) {
        console.log(err);
    }

    // 删除
    try {
        var result = await db.bulkDocs([{
                title: 'Lisa Says',
                artist: 'Velvet Underground',
                _id: "doc1",
                _rev: "1-84abc2a942007bee7cf55007cba56198"
            },
            {
                title: 'Space Oddity',
                artist: 'David Bowie',
                _id: "doc2",
                _rev: "1-7b80fc50b6af7a905f368670429a757e"
            }
        ]);
    } catch (err) {
        console.log(err);
    }

    // 上述操作成功之后的返回值 同 put/post 的返回成员, 包含 _id/_rev/ok
    var result = [{
            "ok": true,
            "id": "doc1",
            "rev": "1-84abc2a942007bee7cf55007cba56198"
        },
        {
            "ok": true,
            "id": "doc2",
            "rev": "1-7b80fc50b6af7a905f368670429a757e"
        }
    ];

    // 如果上述操作产生错误时，将单独返回如下成员
    // 结果以与提供的“docs”数组相同的顺序
    var result = [
        // ...
        {
            status: 409,
            name: 'conflict',
            message: 'Document update conflict',
            error: true
        }
    ];
```

* **注意** `bulkDocs()` 不是事务, 可能会得到一个错误/非错误的混合数组。而在CouchDB/PouchDB中，最小的原子单位是文档

* **注意** 在调用 `bulkDocs()` 时传入 `options.new_edits` 为 false，可以发布来自其他数据库的现有文档，而不需要为它们分配新的修订id，通常，只有[复制算法](https://docs.couchdb.org/en/stable/replication/protocol.html?highlight=new_edits#upload-batch-of-changed-documents)需要这样做

### 批量获取文档

* `db.allDocs([options], [callback])` 批量获取文档，依据指定 `_id` 来索引和排序。Deleted documents are only included if options.keys is specified
* `options` 选项说明，所有的选项均为false除非指定
  + `options.include_docs` : Include the document itself in each row in the doc field. Otherwise by default you only get the _id and _rev properties.
  + `options.conflicts` : 是否在返回值集合成员中包含 `_conflicts` 来展示冲突信息
  + `options.attachments` : 包含 `base64-encoded` 格式的附件数据
  + `options.binary` : 使用 `Blobs/Buffers` 带代替 `base64-encoded` 格式返回附件数据
  + `options.endkey` . Default: true.
  + `options.inclusive_end` : 包含ID等于给定值的文档
  + `options.startkey & options.endkey` : 获取从 `startkey` 到 `endkey` 范围以内的文档， `startkey` 与 `endkey` 表示文档的 `_id` 
  + `options.limit` : 最大返回文档数
  + `options.skip` : 返回前要跳过的文档数量(警告: IndexedDB/LevelDB上的性能很差!)
  + `options.descending` : 反转输出文档的顺序。注意，startkey和endkey的顺序在 `options.descending:true` 时是相反的
  + `options.key` : 只返回 `_id` 与此字符串键匹配的文档
  + `options.keys` : Array of string keys to fetch in a single shot.
    - 这个选项不能与 `startkey` 和 `endkey` 同时使用
    - 返回文档集的成员的顺序与 `options.keys` 的数组序相同
    - 被删除文档将具有删除的修订ID，并且在value属性中有一个额外的键“deleted”:true
    - 不存在的文档的只包含一个 `"error"` 属性，其值为“not_found”
    - 更多详情，请查看 CouchDB 的 [query 选项文档](https://docs.couchdb.org/en/stable/api/ddoc/views.html#db-design-design-doc-view-view-name)
  + `options.update_seq` : 包含一个 `update_seq` 值，该值指示视图所反映的底层数据库的序列id。

**注意**: 用于分页, `options.limit` 和 `options.skip` 是很有用的，但与 `CouchDB` 存在同样的性能担忧，可以使用 `options.startkey&options.endkey` 的方式来代替这一操作

* > 用例

``` js
    try {
        var result = await db.allDocs({
            include_docs: true,
            attachments: true
        });
    } catch (err) {
        console.log(err);
    }

    var result = {
        "offset": 0,
        "total_rows": 1,
        "rows": [{
            "doc": {
                "_id": "0B3358C1-BA4B-4186-8795-9024203EB7DD",
                "_rev": "1-5782E71F1E4BF698FA3793D9D5A96393",
                "title": "Sound and Vision",
                "_attachments": {
                    "attachment/its-id": {
                        "content_type": "image/jpg",
                        "data": "R0lGODlhAQABAIAAAP7//wAAACH5BAAAAAAALAAAAAABAAEAAAICRAEAOw==",
                        "digest": "md5-57e396baedfe1a034590339082b9abce"
                    }
                }
            },
            "id": "0B3358C1-BA4B-4186-8795-9024203EB7DD",
            "key": "0B3358C1-BA4B-4186-8795-9024203EB7DD",
            "value": {
                "rev": "1-5782E71F1E4BF698FA3793D9D5A96393"
            }
        }]
    };

    // 使用 startkey/endkey 查询文档
    // 将返回 _id 从 startkey 到 endkey 之间的文档
    try {
        var result = await db.allDocs({
            include_docs: true,
            attachments: true,
            startkey: 'bar',
            endkey: 'quux'
        });
    } catch (err) {
        console.log(err);
    }

    // 前缀检索
    // 获取符合如下规则的文档
    try {
        var result = await db.allDocs({
            include_docs: true,
            attachments: true,
            startkey: 'foo',
            endkey: 'foo\ufff0'
        });
    } catch (err) {
        console.log(err);
    }
```

**检索备注** 在 `CouchDB/PouchDB` 中文档的 `_id` 是按照[字典顺序](http://docs.couchdb.org/en/latest/couchapp/views/collation.html)来存储的.

* > 结果集说明
    - `total_rows` 数据库中未删除文档的总数
    - `offset` 如果提供了 `options.skip` ，则使用CouchDB中的实际偏移量
    - `rows` 包含的文档，或者如果没有将 `options.include_docs` 设置为true，则只有 `_id/_revs` 
    - `update_seq` 如果将 `options.update_seq` 设置为true，也将包含 `update_seq` 

