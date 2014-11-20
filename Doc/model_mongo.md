## 模型（MongoDB）

thinkjs从`1.1.0`版本开始支持`MongoDB`，底层依赖了`mongoose`模块，所以使用MongoDB时候需要在项目下安装`mongoose`模块。

```
npm install mongoose
```

### 配置

```js
db_type: 'mongo', // 数据库类型
db_host: '127.0.0.1', // 服务器地址
db_port: '', // 端口
db_name: '', // 数据库名
db_user: '', // 用户名
db_pwd: '', // 密码
```

### 创建模型

MongoDB的模型必须继承自`MongoModel`，如：

```js
module.exports = Model('MongoModel', {
    //字段列表
    fields: {
      title:  String,
      author: String,
      body:   String,
      comments: [{ body: String, date: Date }],
      date: { type: Date, default: Date.now },
      hidden: Boolean,
      meta: {
        votes: Number,
        favs:  Number
      }
    },
    //mongoose模块里创建Schema的选项
    schema_options: {

    }
})
```

### CURD操作

MongoDB模型的CURD操作封装的接口和Mysql下一致。参考 [CURD操作](/doc/model.html#curd操作)。

### 其他

MongoDB模型目前封装的接口还比较少，可以根据项目需要自己封装接口。可以使用下面的一些方法：

#### initDb()

获取MongoDB的连接句柄。

#### model()

获取当前模型的实例。如`add`方法的实现：

```js
add: function(data){
    return this.model().then(function(model){
      var instance = new model(data);
      var deferred = getDefer();
      instance.save(function(err){
        if (err) {
          deferred.reject(err);
        }else{
          deferred.resolve();
        }
      })
      return deferred.promise;
    })
}
```

#### thinkRequire('mongoose')

在模型中，可以通过`thinkRequire('mongoose')`来加载`mongoose`模块。

-------------

更多使用方式请见 [mongoose文档](http://mongoosejs.com/docs/guide.html)。
