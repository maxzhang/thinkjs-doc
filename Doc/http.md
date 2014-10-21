## http对象

这里讲的http对象并不是Node.js里的http模块，而是thinkjs里将http请求的Request和Response 2部分包装在一起的一个对象。

由于Node.js是启服务的方式运行，所以处理用户请求时必须将当前请求的Request和Response对象向后续的处理逻辑里传递，比如：express里有req, res对象。thinkjs里为了方便处理，将Request和Response包装成了一个http对象。

### 传递路径

http对象会在下面的功能模块会中传递：

* `Behavior` 行为类
* `Controller` 控制器类

控制器类会在init方法里将传递过来的http对象赋值给this.http。

```js
module.exports = Class({
    init: function(http){
        this.http = http;
    }
})
```

项目中的控制器类会继承控制器基类，如果要重写init方法，那么必须调用控制器基类的init方法，并将http对象传递过去。如：

```js
module.exports = Controller({
    init: function(http){
        this.super("init", http);
        //其他逻辑
    }
})
```

### 处理的数据

http对象里包含了很多处理用户请求的数据，如：cookie数据，get参数，post内容，上传的文件等等。

#### cookie数据

解析的cookie数据存放在`http.cookie`对象里，在`Controller`里直接通过`cookie`方法获取即可。

#### get参数

解析的get参数存放在`http.get`对象里，在`Controller`里直接通过`get`方法获取即可。

#### post内容

post内容在不同的场景下可能有不同的数据格式，thinkjs提供多种的解析方式。

##### querystring解析

thinkjs默认使用querystring的方式来解析post的内容，如：

```js
name=welefen&value=111
```

解析后的post数据为：

```js
{
    "name": "welefen",
    "value": "111"
}
```

##### json解析

对于复杂的数据，querystring解析就不合适了。这时候浏览器端可以传递一个json的数据格式，服务端也用json的方式来解析。

thinkjs支持在发送数据的时候指定特定的`Content-Type`来使用json解析，默认的`Content-Type`为`application/json`。

可以通过下面的参数来修改：

```js
post_json_content_type: ['application/json'], //post数据为json时的content-type
```

配置值为数组，这样就可以指定多个Content-Type来使用json解析。

##### 自定义方式解析

thinkjs除了支持使用querystring和json的方式来解析post数据外，还可以使用自定义的方式解析。thinkjs是通过行为切面来完成这一功能的，具体的行为切面名称为`form_parse`。

可以在项目的App/Conf/tag.js里指定行为切面`form_parse`对应的行为进行解析的工作。如：

```
//解析xml格式的数据
var xmlParse = function(http){
    var postData = http.payload; //post数据在http.payload里
    //解析xml格式数据的逻辑，并将解析的结果返回
    //返回的可以是个promise
}
module.exports = {
    form_parse: [xmlParse]
}
```

#### 上传的文件

thinkjs支持表单文件上传和ajax文件上传2种方式，解析后的数据放在`http.file`对象里，`Controller`里直接使用file方法获取即可。

##### 表单文件上传

表单文件上传可以指定如下的配置参数：
```js
post_max_file_size: 1024 * 1024 * 1024, //上传文件大小限制，默认1G
post_max_fields: 100, //最大表单数，默认为100
post_max_fields_size: 2 * 1024 * 1024, //单个表单长度最大值，默认为2MB
post_file_upload_path: APP_PATH + '/Runtime/Temp', //文件上传的临时目录
```

##### ajax文件上传

高级浏览器下支持使用ajax来上传单个文件，如：
```js
var xhr = new XMLHttpRequest();
xhr.onreadystatechange = function(e){
  
}
xhr.open("POST", '/admin/project/upload', true);
xhr.setRequestHeader("X-FILENAME", 'file');
xhr.send($('#fileField')[0].files[0]);
```
对于ajax上传的内容，thinkjs是通过下面的配置来判断上传的是否是文件：

```js
post_ajax_filename_header: 'x-filename', //通过ajax上传文件时文件名对应的header，如果有这个header表示是文件上传
```
该头信息的值作为具体的文件名来获取。

通过ajax上传的文件filedName固定为file，在Controller中可以通过`this.file("file")`来获取。

获取到单个文件的信息如下：

```js
{
    fieldName: 'file',
    originalFilename: filename, //原始文件文件名
    path: filepath, //文件存放的临时目录
    size: fs.statSync(filepath).size //文件大小
}
```

### 其他

#### 原始的Request和Response对象

http是一个包装的对象，但保留了原始的Request和Response对象，可以通过`http.req`和`http.res`来获取。