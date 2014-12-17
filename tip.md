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

- 不要扩展内置对象，应该像 underscore 那样（除非网站的代码全部由你控制，当然也不引入第三方代码），
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

##2014-10-28

- 将jQuery/zepto插件改造成CMD/AMD模块，重要的一点是 jQuery/zepto 的名字有惯例，
  针对CMD/seajs，jQuery别名为jquery和$，而针对zepto命名为zepto和$，如此下面代码：

  ```js
  // https://github.com/hammerjs/jquery.hammer.js/blob/master/jquery.hammer.js
  (function(factory){
    if (tyepof define === 'function' && define.cmd) {
      define('', ['jquery'], factory);
    } else if (typeof exports === 'object') {
      factory(require('jquery', require('pluginjs'));
    } else {
      factory(jQuery, Hammer);
    }
  }(function($, Plugin){
    // plugin main code
  }));
  ```

- zepto的animate方法，或者说动画插件有问题，它检测是否支持transform时依赖的是transition，
  应该分开检测的，参考jquery.transform.js插件的检测方法。另外下面这种写法：

  ```js
  $('#box').animate({
    transform: 'translateY(200px)'
  }, 200);
  ```

  应该在Android 4.4及以前的浏览器中加 vendor 前缀的

  同样，zepto的css好像也有此问题，待查看源代码

##2014-10-29

- force reflow/trigger reflow:

  ```js
  // Native
  (element.offsetWidth);

  // jQuery
  $(elem).offset().left;

  // Zepto $.fx.js
  // Don't understand why need the latter part of the below statement
  $(elem).size() && $(elem).get(0).clientLeft;
  ```

  Ref:
  http://stackoverflow.com/questions/510213/when-does-reflow-happen-in-a-dom-environment
  http://stackoverflow.com/questions/9016307/force-reflow-in-css-transitions-in-bootstrap

- get The current value of `transform`:

  ```js
  // It maybe return a non-matrix string with zepto's(1.1.3) css method in Firefox. ('translate(0px, 200px)')
  window.getComputedStyle(elem)['WebkitTransform'];
  ```

- Extract a matrix string:

  ```js
  // #1
  var matrix = 'matrix(1, 0, 0, 1, 100, 200)';
  matrix = matrix.split(/[(,)]/);
  var m = {
    a: parseFloat(matrix[1]),
    b: parseFloat(matrix[2]),
    c: parseFloat(matrix[3]),
    d: parseFloat(matrix[4]),
    e: parseFloat(matrix[5]),
    f: parseFloat(matrix[6])
  };

  // #2
  var matrix = 'matrix(1, 0, 0, 1, 100, 200)';
  var m = new WebKitCSSMatrix(matrix);
  ```

##2014-10-31

- Use for...in to enumerate the properties of an object, not to iterate the elements of an array.
  The for loop is better, and faster than while loop.

- Asymmetric animation timing (in generally):

  - For UI animation triggered by a user's interaction, such as view
    transitions or showing an element, have a fast intro (short duration),
    but a slow outro (long duration).
  - For UI animation triggered by your code, such as error or modal views,
    have a slower intro (longer duration), but a fast outro (short duration).

  Source: [https://developers.google.com/web/fundamentals/look-and-feel/animations/asymmetric-animation-timing](https://developers.google.com/web/fundamentals/look-and-feel/animations/asymmetric-animation-timing)

  PS: Google或其他大公司有不少好文档

- ease-out 最自然，就好像自然界的事物一样，渐渐停下来，而 ease-in 是逐渐加速离去，
  实际上可以使用比 ease-out 更大的加速度，考虑 back-to-top 功能，如果页面高度比较大，则越快滚动到底部越好，
  但停下来时也要有个缓冲的过程，直接跳到顶部太突兀。

- 选择合适的动画 easing: http://easings.net

  选择合适的动画持续时间(duration)，下面仅供参考：

  - ease-in/ease-out: 200-500ms
  - bounce/elastic: 800-1200ms

- 屏幕的大小可能对动画的某些参数，如持续时间，延迟等要求不同

- simple css-based offscreen/offcanvas tech: 

  [https://developers.google.com/web/fundamentals/look-and-feel/animations/animating-between-views](https://developers.google.com/web/fundamentals/look-and-feel/animations/animating-between-views)

- 跨设备跨系统同步配置文件：[http://dotfiles.github.io/](http://dotfiles.github.io/)

  一直觉得配置好常用的系统、编辑器等是一件十分划算的事情，只需要累计花几天时间，却可以伴随三五年，或者更长。

##2014-11-03

- grunt-contrib-uglify 从 0.6.0 开始支持 ASCIIOnly 参数（默认为 false），用来指明是否将 non-ASCII 转换成 \uxxxx 形式。
  但是不支持 JS 文件为 GBK 编码。

##2014-11-04

- IE “缺少标识符、字符串或数字” 可能是由于对象或数组字面量的最后一项后面加了逗号，要注意 IE 调试工具报错的行数与实际出错行数可能相差十万八千里。
  另外可能还会报一个“由于出现错误80020101而导致此项操作无法完成”错误，我的是报了 jQuery-1.6.4的某行，但是实际上没有问题的。

  可以通过编辑器的多文件正则(,\n\s*[\]\}])查找所有js文件及`<script>`标签内的错误代码

- 插件的样式依赖某个全局样式时会有被覆盖的风险，可能在某些页面中，有人覆盖了全局样式，而正好插件又依赖此样式，此时会导致插件的样式改变。
  限制人覆盖全局样式，得要求全局样式简单，就像normalize.css，因为有些人根本就不看全局样式文件是什么，只管写自己的，用工具检测？要不插件就不要依赖全局样式。

- 判断一个用户名（最长15位）是否符合“中英文、数字、下划线“采用正则更简洁，且性能更好。
  不是说正则不适合判断字符范围吗（在判断一篇文章的长度，英文为1，中文为2，的时候采用循环更快啊）

##2014-11-05

- jshint的配置文件为`.jshintrc`导致github不能识别是什么语言，还不如直接命名 jshintrc.js 或 jshintrc.json 之类呢

##2014-11-10

- 鼠标移到button上，处于:hover状态，点击弹出一个遮盖层，鼠标变成遮盖层设置的通常为默认状态，
  假如此时遮盖层消失，则鼠标在button上面，button在IE8中为默认状态，没有激活:hover样式，在chrome就正常

- 打开或关闭开发者工具（内嵌方式）会触发 resize 事件，当然通常的用户不会这样做的

- 如果只是写个小的插件，且master与gh-pages分支内容大部分一样，那就全部一样了，推送两次就可以了。

##2014-11-12

- （jobcn）SVN上面checkout一个项目时，需要配置：
  - nginx
  - resin
  - Debug Configurations
  - Configure Build Path
  - Java Compiler - Compiler compliance level
  - Content-Type - Text - Java Source File - Encoding
  - ...

##2014-11-14

- HTML5规范中，<a>标签可以包含一些块级元素，如 div, p, section 等，HTML4.01中<a>不可以包含块级元素，无法通过验证
  那对于 screen reader 和搜索引擎影响呢？
  http://www.w3.org/TR/html5/text-level-semantics.html#the-a-element
  http://stackoverflow.com/questions/1827965/is-putting-a-div-inside-an-anchor-ever-correct
  http://stackoverflow.com/questions/1091739/html-div-in-link-problem

- 调试JS时，如果错误发生在某个断点处，应该往调用栈的上面继续打断点调试

- 一定要调试临界值，可以模拟后端数据，即直接附给变量临界值

##2014-11-15

- 区分好登录与未登录，有些信息与功能只有登录才能用时，那就不要未登录时就加载，等到登录时在加载

##2014-11-19

- 用来显示数据的标签，如果数据没有时，应该连标签也不要显示，而不仅仅不显示文本，当然也有例外，取决于样式设置

##2014-11-20

- JS/CSS让两个缺乏沟通的人写时，共同依赖的HTML结构与class名称不一样，这样在写UI插件时提供自定义class就有必要了

- 比如一个slider插件，如何决定从第几张开始：

  - HTML的class决定，slider会自动探测
  - slider插件初始化时指定从第几张开始
  - 结合上面两种方法，第二种JS指定的优先级高些

##2014-11-21

- 后代选择器写样式，要比子代选择器对结构要求低，方便与JS插件结合使用，特别是CSS/JS两个人写时

##2014-11-24

- 在IE8中，点击带href属性的a标签时，会触发 beforeunload 事件(href属性只改变hash时不会）
  解决方法：阻止默认事件，如下代码：

  ```js
  $(document).on('click', 'a.unlink', function(e) {
    e.preventDefault();
  });
  ```

  ```js
  // 针对新生成的，别人代码中的，无法添加unlink的
  $(document).on('click', 'a', function(e) {
    // 判断href属性以javascript:开头的，则阻止默认事件
  });
  ```

- 在IE8中，下面的CSS会报错，说是引起权限问题（IE8不支持otf格式）：
  @font-face{font-family: 'Helvetica';src: url('/info/style/Helvetica.otf');}

- IE6-8中，如果超链接是ajax加载后渲染的，点击超链接的虚线边框怎么去除

- 一个（业务）模块中的全局变量管理，通常总是会有一些全局/模块级变量的，对于全局的字符串/数字等

- jQuery.ajax获取数据时，如果不加刷新缓存的时间戳，则在IE8中会拿到缓存的数据，不会发送请求，在Chrome里可以获取最新数据

- 开启输入法状态下，keydown/kepress/keyup是否触发，在不同浏览器表现不同，不同输入法可能也不同（这个未经验证），在做类似google下拉提示那种功能时，采用计时器方案更可靠，focus时激活，blur时解绑

##2014-11-25

- 模块，方法注明作者（有地方查询），所有改动均需要留下自己名字，任务编号呢？

- 在IE11中，form默认提交（同步）会停掉当前页面动画gif的播放，跳转链接时呢？

##2014-11-26

- 绝对定位四个方向都设为0来实现宽高100%时在IE7+才起作用，IE6中不行，IE6中就像浮动了一样，必须设`width:100%;height:100%`

##2014-11-27

- 想一下这种场景，分页显示一些数据，这些数据可以单个或批量删除，然后点击删除时，向后端发送请求，
  返回成功状态后，前端应该做些什么？当前页显示的总数据条数不变，那么应当用后面的填充，
  这就要后端返回与删除数目相等的数据，这样的话，是直接返回当前页数据好呢？还是返回相等数目的（按一定排序规则）？

##2014-11-28

- SVN不能提交，显示已存在该文件夹时，可能是该文件夹下存在`.svn`目录

##2014-11-29

- Bower Packages:
  初始化、安装、发布同npm基本一样，bower.json格式同package.json格式也基本一样。有一个发布不同点：bower没有用户名这一说，而且代码是利用的github上面的。

##2014-12-01

- 终于体会到 partial template 的用处了，优势就是可以提高初次渲染速度，当然能前后端共用模板就最佳了

##2014-12-02

- git忽略某个目录中除某个文件之外的所有子目录和文件：


  ```
  *
  !foo.html
  ```

  下面的不起作用：

  ```
  !foo.html
  ```

  或

  ```
  ./
  !foo.html
  ```

- shell 将一段文本写入文件：

  ```bash
  # 覆盖掉原内容
  echo 'hello, world' > foo.txt

  # 追加内容至文件尾部
  echo 'hello, world' >> foo.txt
  ```
 
- 将html文档转换成pdf文档（公司一个java类）时，font-family必须使用字体名称（非文件名称），用引号包裹

- 可以通过模板语言的语法实现将某个引入的模块（比如网站的搜索模块）中的js代码（只是script标签中的引入代码）放到body前面，
  但是script放中间有性能损失，两者做法对比？

- 任何后端语言多可以实现与前端javascript共用一套模板

##2014-12-03

- 在远程编辑文件时，最后有本地备份，不知为什么使用Sublime（直接编辑远程服务器上的文件，mount过来的）保存成功，
  因为远程服务器上的网页确实变了，但是第二天远程服务器重启了，结果改动不见了

##2014-12-05

- WebJava + eclipse + nginx 时，有时脚本无法访问（403错误）可能是文件权限问题，递归设置下项目文件夹

 ##2014-12-07

- escape/unescape: 早期的非标准方法，会将非ASCII字符转义为%uhhhh形式；

- encodeURI/decodeURI与encodeURIComponent/decodeURIComponent区别：
  后者会比前者多转义一些字符，常用于各自转义querystring的key与value。

  ```js
  var ch;
  var arr = [];

  for (var i = 0; i < 256; i++) {
    ch = String.fromCharCode(i);
    if (encodeURI(ch) !== encodeURIComponent(ch)) {
      arr.push({
        charachter: ch,
        encodeURI: encodeURI(ch),
        encodeURIComponent: encodeURIComponent(ch)
      });
    }
  }

  console.table(arr);
  ```

##2014-12-08

- iframe上传文件：
  
  - 跨子域：需要返回一个html页面，然后执行一行语句`document.domain=*.com`
  - 跨全域：需要跳转到与包含iframe页面相同域名下的某个页面

- 修改seajs模块的文件名时，记得考虑alias/path以及引用该模块的文件。这个应该单独一个jira号或无jira号，
  反正不应该混入到未上线的投稿系统任务中，导致seajs配置文件上线了，但是模块文件未上线，引用不到模块，
  导致js报错，导致页面数据不显示的严重bug

##2014-12-09

- favicon request: 如果未指定favicon，在请求页面（包括iframe）时，都会产生一个根目录下的 /favicon.ico 请求
  ，且通常该请求带有cookie。为了提高性能，可以采用下面的做法：

  - 绝对不要返回404
  - 可以将favicon图片尽可能减小，小于1kb，并且设置合理的缓存时间，参考：
    https://developer.yahoo.com/performance/rules.html#favicon
  - 可以使用base64编码：
    `<link rel="icon" href="data:;base64,iVBORw0KGgo=">`
    ，参考http://stackoverflow.com/questions/1321878/how-to-prevent-favicon-ico-requests#answer-13416784

##2014-12-10

- Win7原生IE9中，如果一个固定定位元素的margin-left为auto，其父元素margin-left也为auto，则该元素的margin-left等于其父元素的margin-left值

  获取正确margin-left值的方法，可使用jQuery的offset().left ，假设固定定位元素为#inner，其父元素为#outer，则：

  ```js
  $('#inner').offset().left - $('#outer').offset().left - $('#outer').css('borderLeftWidth');
  ```

  另外也可以坚持，margin: auto不与固定或绝对定位同时使用。

  还有，固定定位元素的 `offsetParent` 返回 `null`（Webkit中）

##2014-12-15

- iframe页面的script标签中的脚本里，在Ubuntu 14.04 - Firefox 34 中后声明的函数先使用时会报引用错误，
  显示函数未定义，即没有函数声明提升的效果

##2014-12-16

- IE6-8中，新创建的iframe的onload属性不能被赋值，必须通过IE的attachEvent来绑定onload事件。

##2014-12-17

- 一个操作元素的方法，比如`removeClass(elem, classes)`，假设调用该方法时元素为`null`呢，
  是否在该方法中检测元素是否存在呢？还是让调用该方法的人保证元素必然存在？如果是选择后者，那么元素不存在，
  仍然调用该方法就会报错，可以利用try..catch捕获错误让代码继续执行，不过还是检测更好，但是可能影响效率

