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

