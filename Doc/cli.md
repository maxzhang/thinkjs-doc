## 命令行模式

thinkjs无缝支持命令行模式的调用，控制器的逻辑可以和普通HTTP请求的逻辑完全一致，可以做到同一个接口即可以HTTP访问，又可以命令行调用。

比如要执行IndexController里的indexAction，可以使用如下的命令：

```shell
node index.js /index/index
```

如果需要带上参数，可以直接在后面加上对应的参数即可，如：

```shell
node index.js /index/index?name=welefen
```

也可以是：

```shell
node index.js /index/index/name/welefen
```

### 修改请求方法

命令行执行默认的请求类型是GET，如果想改为其他的类型，可以用下面的方法：

```shell
node index.js url=/index/index&method=post
```

这样就把请求类型改为了post。但这种方式下，参数url的值里就不能包含&字符了（可以通过上面/的方式指定参数）。

除了修改请求类型，还可以修改下面的参数：

* `host` 修改请求的host 默认为127.0.0.1
* `ip` 修改请求的ip 默认为127.0.0.1

### 修改更多的headers

有时候如果想修改更多的headers，可以传一个完整的json数据，如：

```shell
node index.js {"url":"/index/index","ip":"127.0.0.1","method":"POST","headers":{"xxx":"yyyy"}}
```

注：参数比如是一个合法的json数据结构。


### 超时机制

命令行模式下执行的操作一般都是比较耗时的，如果代码不够严谨，可能会导致进程一直没有结束的情况。thinkjs从`1.1.7`版本开始增加了超时的机制。可以通过下面的配置来指定超时的时间：

```
cli_timeout: 60 //超时时间，单位为秒
```
