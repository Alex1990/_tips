##2014-09-28

- 发现 grunt-seajs-fresh 要兼容 seajs 配置的各种情况，真的很复杂，有必要考虑所有情况吗？
    (兼容paths与vars，兼容alias与map不是很难，可能极端情况较少考虑）

##2014-09-30

- nodeunit很不好用，废弃之，作者的一篇教程已经过时，且至少有两个错误，
    错误很难解决，用得人少，作者也不理

##2014-10-01

- [XP/Chrome36]使用@font-face来引入font-icons时，fontello网站下载，
    有时第一次**File协议打开页面**时，加载了字体但没有显示，有时快速刷新，
    大概十次有一次字体没有显示，**不过，本地服务器就没出现这种情况**。

##2014-10-08

- node update：`npm install -g n` then `n 0.10.32`，另外也可以由高版本切换到低版本

- 大概从1.4.13版本开始，npm 默认安装包时不会显示请求，可以：

  ```bash
  npm install --loglevel http
  ```
  或修改.npmrc：

  ```bash
  loglevel = http
  ```

- nodejs中的fs.exists尽量不要使用，使用fs.open检测失败就可以了，另外fs.exists可能被
  放弃，某些版本不存在可以使用path.exists

- `mkdir -p`或者说node-mkdirp之类递归创建目录，可能参数：

  ```bash
  mkdirp path
  mkdirp [mode] path
  mkdirp path1 path2
  mkdirp path1 path2/subpath2
  mkdirp .
  mkdirp ..
  ```

- remove BOM: `text.replace(^\uFEFF/, '');`

- 将windows[\r\n]或osx[\r]系统下换行符替换成linux[\n]下换行符:

  ```js
  if (text.indexOf('\r') > -1) {
      text.replace(/\r/g, '');
  }
  ```

##2014-10-09

- markdown list multiple paragraphs:

  like this:)

- 函数的某个参数，通常不指定，则为 undefined，若要其通常为 true，则
  需要加上：

  ```js
  if (parameter == null) parameter = true;
  ```

  但是，如果参数只是用来判断的布尔值，何不反向判断，让参数含义也反过来
  
  ```js
  function camelize(str, capitalize) {
    if (capitalize  == null) capitalize = true;
    // ...
    if (capitalize) {
      // ...
    }
  }
  ```

  to:

  ```js
  function camelize(str, notCapitalize) {
    // ...
    if (!notCapitalize) {
      // ...
    }
  }
  ```

##2014-10-10

- jQuery(至少1.6以前): `$(document).width()` 在IE8中包含滚动条，应该用`$(document.body).width()`

- API design: 利用别名来增加灵活性，如：

  ```js
  exports.word_wrap = exports.wordWrap = function(text, width){
    // ...
  };
  ```

##2014-10-12

- 不要扩展内置对象，应该向 underscore 那样（除非网站的代码全部由你控制，当然也不引入第三方代码），
  公司以前代码扩展了 Array.prototype.indexOf，而百度分享代码有个 extend 方法，使用了 for...in
  且没有检测是否是原型对象上的属性，从而把数组的所有可遍历属性都复制了，从而在IE8-中把数组原型上添加的属性也复制了，
  从而导致了点击分享上“更多”按钮时会报错。
  还有一个原因是因为IE8-中不可以设置对象（IE8中仅DOM对象可以）的属性为不可遍历（non-enumerable）

##2014-10-15

- nodejs 程序开机启动（startup)，利用/etc/rc.local:

  ```bash
  exec 2> /tmp/rc.local.log #把stderr发送到此文件
  exec 1>&2                 #把stdout发送到此文件
  set -x                    #告诉sh，在执行前显示参数
  cd /the/path/to/app
  /usr/local/bin/node app.js &

  exit 0
  ```

  错误形式：

  ```bash
  #1
  node /the/path/to/app/app.js &

  #2
  cd /the/path/to/app && $(which node) app.js &

  #3
  $(which node) /the/path/to/app/app.js &

  #4
  /usr/local/bin/node /the/path/to/app/app.js &
  ```

- JS/CSS代码开发与生产版本切换：通过传递参数切换，可以通过在head用
  内嵌JS实现或者后端代码实现

  三种版本：线上压缩、线上未压缩、本地未压缩

- 参数的默认值：使用`if`比`||`效率高20%-40%

  http://jsperf.com/default-values-if-vs-or-operator

##2014-10-16

- 使用nodemailer时，配置的host拼写错误，但是又没有报错，导致一两个小时
  不知道什么原因，拼写错误一直伴随着

##2014-10-17

- mongoose的find/findOne等方法的查询结果为mongoose内部定义的对象，
  直接添加属性时，可console.log出其属性值，但是console.log该对象时，
  不会显示添加的属性，需要使用toObject()方法转为普通的对象

- 应用的配置信息 config.js，开发版与生产版一样，使用git同步，但是可以
  有一个 selfConfig.js 用来定义那些不同的配置，可以采用类似于
  `$.extend(config, selfConfig)`方式获取最终配置

##2014-10-19

- JS中的函数重载技术（function overloading)在API设计方面很有用，比如：

  jQuery中很多getter/setter方法：

  ```js
  $(element).css(name); // Get the value of the property
  $(element).css(name, value); // Set a property
  $(element).css(keyValue); // Set a group of properties
  ```

  下面也经常使用：

  ```js
  function(data, replace, callback){
    if (!callback) {
      if (typeof replace === 'function') {
        callback = replace;
        replace = false; // The default value of replace
      } else {
        callback = function(){};
      }
    }
    // ...
  ```

##2014-10-20

- mongoose relationship/(foreign key): use populate() method,
  and set the Schemas like the below:
  ```js
  var UserSchema = new Schema({
    username: {type: String, required: true},
    password: {type: String, required: true},
    posts: [{type: Schema.ObjectId, ref: 'Post'}]
  });

  var PostSchema = new Schema({
    title: {type: String, required: true, default: ''},
    content: {type: String, required: true, default: ''},
    user: {type: Schema.ObjectId, ref: 'User'}
  });
  ```

##2014-10-21

- 插件调用方式：

  符合jquery ui或bootstrap那样的调用方式:

  ```js
  $(selector).plugin(options);
  ```

  或者

  ```js
  var plugin = new Plugin(options)
  ```

  反正不要分成好几步，实例化/配置/初始化一部到位

- 插件使用了多次，但是主题不同，最好能提供一个类似于命名空间的class用来覆盖默认主题，
  当然你也可以在body标签或一个容器元素上添加命名空间class

- 假如一个弹出层插件依赖一个遮罩插件（采用模块化细颗粒度开发），那么弹出层插件在调用时
  是否传入遮罩插件的配置参数呢？比如遮罩的颜色或动画效果。

- meta property og image是 Facebook Open Graph API，用于定制点击FB的like
  或recommend按钮时的标题/图片/url等：
  
  https://developers.facebook.com/docs/graph-api

- click事件在Android Chrome中几乎没延迟，在微信中有延迟，而且使用了zepto的tap事件
  仍然和使用click差不多延迟（凭感觉），另外要注意简单的把click替换为tap事件可能会破坏程序的功能

- jQuery html vs text：text()会将特殊字符转换为实体符号

  **性能**：如果是纯文本，html会比text快一倍，然而使用innerHTML会比html()快三倍，
  使用nodeValue会比innerHTML快20倍，可见原生JS效率还是比jQuery快很多的

##2014-10-22

- UC9.9无法使用meta标签使其不能缩放

  **Note**: touchmove 事件时，`Touch.clientX/clientY/PageX`等只会改变一次，需用调用`preventDefault()`
  阻止浏览器的默认行为

##2014-10-23

- 插件提供全局默认设置接口，这在多次调用插件时，很有用，因为有一部分配置参数相同，而另一部分配置参数需要单独配置:

  ```js
  Box.defaults = {
    class: 'namespace-class',
    template: $('#select-box-tpl').html()
  };

  Box.defaults.template = $('#alert-box-tpl').html();
  ```

- 配置对象的合并方法：`$.extend({}, defaults, opts)`,第一个空对象参数是为了每次调用都实例化一个对象

  下面是不好的做法：

  ```js
  var defaults = {
    // ...
  };
  opts = $.extend(defaults, opts);
  ```

  因为合并后返回的对象是 `defaults`，最终`opts`为指向`defaults`对象的引用，假如有一个Box插件，两次调用参数：

  ```js
  var box1 = new Box({
    id: 'box1',
    onopen: function(){
      alert(1);
    }
  });
  var box2 = new Box({
    id: 'box1',
    onopen: function(){
      alert(2);
    }
  });
  ```

  以上实例化两个Box之后，再触发 box1 的 open 事件，调用的 onopen 会变成 box2 的，就是因为实例化 box2 时，
  配置对象覆盖了第一个，

##2014-10-24

- Number() vs parseInt(): 

  相同点：都能解析完全符合十六进制的字符串

  不同点：Number不会把`'012'`当成八进制，还有
  
  ```js
  Number('0xaaz'); // NaN
  Number('12a'); // NaN
  parseInt('0xaaz'); // 170
  parseInt('12a'); // 12
  ```

  注意：parseInt('012')在IE8-中与IE9+表现不同，因为在ES5中不会把0开头的字符串当成八进制

  ```js
  // IE6-8
  parseInt('012'); // 10

  // IE9+及其他现代浏览器
  parseInt('012'); // 12
  ```

- 十进制数字字符串转换成数值类型，使用一元符号`+`：`+'12'`, `+'-100'`

##2014-10-27

- zepto：触发tap事件后，会在大约300ms（猜的）后，在相同位置触发一个click事件，且target为相同位置下面的任何元素，
  如果恰好有一个元素移动到此位置，位于触发tap事件元素的上面，且该元素绑定了click事件，则会触发此click事件。

  解决方案：

  1. 两个元素使用相同的事件，如都使用tap/click
  2. 触发tap事件的元素使用 singleTap 绑定

- input:checkbox：使用背景图片结合-webkit-appearance:none; 来替换默认的图标，则在iPhone4s/MX3/Mi3的微信5.4上，
  发现选择时反应有延迟，在Chrome Android就不会

