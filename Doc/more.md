## 更多功能

### 自定义http服务

thinkjs默认会自动创建`http`服务，如果默认创建的服务无法满足需求时，可以通过下面的方式自定义创建`http`服务。

1) **开启自定义服务配置**

在`App/Conf/config.js`文件中添加下面的配置：

```js
create_server_fn: 'create_server_fn_name',
```

其中字符串`create_server_fn_name`是函数名，可以根据需求修改。

2) **自定义创建服务实现**

在`App/Common/common.js`文件中添加如下的代码：

```
//函数名create_server_fn_name需要跟配置create_server_fn值一致
global.create_server_fn_name = function(App){
      var server = require('http').createServer(function (req, res) {
        thinkRequire('Http')(req, res).run().then(App.listener);
      });
      server.listen(C('port'));
      //这里可以加入项目的扩展代码
}

```

`注意：` 修改完成后，重启Node.js服务才能生效。


### 使用cluster

如果项目想使用cluster的话很简单，只要在`App/Conf/config.js`文件中添加如下配置即可：

```
//值为true表示实现当前cpu的个数，可以根据需要更改为实际的数字
use_cluster: true
```

### 禁止通过端口直接访问

Node.js启动的服务默认可以通过`ip+端口`直接访问。但项目上线后，一般是通过nginx做一层反向代理，这样可以很好的做负载均衡，这个时候我们就不希望可以通过`ip+端口`能够直接访问到Node.js的服务了。

thinkjs下可以在`App/Conf/config.js`里添加如下的配置禁止通过端口直接访问：

```
use_proxy: true
```

### 查看当前项目运行的thinkjs版本

可以通过下面的方式查看当前项目运行的thinkjs版本。

![](http://thinkjs.qiniudn.com/view-thinkjs-version.jpg)