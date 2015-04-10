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

- 表单验证组件及写业务时，考虑到规则发生变化的情况

- frecency algorithm: combine frequency and recency

  一种推荐排序算法，Mozilla用于自己浏览器地址栏自动完成功能，其实编辑器的代码自动完成也可以利用这种算法

  https://developer.mozilla.org/en-US/docs/Mozilla/Tech/Places/Frecency_algorithm

- ajax加载的内容，如分页时，且通过改变hash值来标记第几页时，百度不会收录，可以将页码链接写成参数形式，
  后台可以根据这些参数生成相应页面，这样百度可以收录，而前端可以根据这些参数，通过ajax加载相应页面，
  对于低版本浏览器改变url的hash值

##2014-12-18

- IE查看页面编码：右击菜单里面

- ssh-copy-id: command not found in Mac OSX
  
  google or https://github.com/beautifulcode/ssh-copy-id-for-OSX

##2014-12-19

- jQuery delegate可以使用`event.stopPropagation()`, `event.stopImmediatePropagation()`或`return false;`
  来阻止事件冒泡，但是jquery版本必须>=1.7.0

- node listen on port 80:

  Linux中，监听在<=1024端口需要root权限，而且其他程序已经占用了80端口，可使用下面命令：

  ```bash
  sudo iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 3000
  ```

  Ref:

  - http://stackoverflow.com/questions/16573668/best-practices-when-running-node-js-with-port-80-ubuntu-linode#
  - http://stackoverflow.com/questions/413807/is-there-a-way-for-non-root-processes-to-bind-to-privileged-ports-1024-on-l

- 查看某一占用某一端口的程序：`netstat -plnt | grep "80"`

##2014-12-20

- fast-forward merge：不会保留feature branch，只保留master分支，这样以后看不出这个repository以前的具体合并分支，
  可以通过`--no-ff`参数不使用 fast-forward merge。

##2014-12-23

- hash不会发送到服务端

- ubuntu .bashrc中将路径赋值给一个变量，路径不要带引号

- jQuery events namespace & custom events：
  记住点号代表事件名称的命名空间，主要用于触发（trigger）或解绑（off）事件时更精细地区分，如下面：

  ```js
  $el.on('event.oneNamespace.twoNamespace', function(e) {
    // Do some stuff
    console.log(e.namespace);
  });

  $el.trigger('event'); // Fire the handler
  $el.trigger('event.oneNamespace'); // Fire the handler
  $el.trigger('event.twoNamespace'); // Fire the handler
  $el.trigger('event.threeNamespace'); // Do not fire the handler

  $el.off('event.oneNamespace'); // Unbind the handler
  $el.trigger('event'); // Print the empty string
  $el.off('event'); // Unbind all the handlers belong to `event`
  ```

  有时可能是想用自定义事件，但是并不打算用事件命名空间，就不要用点号分隔，可以使用冒号/下划线/连接符等

##2014-12-24

- 防止恶意注册：仅靠前端验证很难拦住，只能加点儿难度，手机号码注册可以比较有效防止吧？

- jQuery.extend()参数情况：

  - 参数只有1个或参数值有2个且第一个参数值为`true`时，复制参数属性到jQuery对象上面，返回值为jQuery对象本身
  - 只有第一个参数`deep===true`时才深复制
  - 可复制对象或数组，深复制对数组也会处理
  - 对象属性值或数组元素值为`undefined`时不复制属性
  - 对于多个参数，会忽略`null`/`undefined`参数，接着处理后面的参数
  - 没有使用hasOwnProperty来检测
  - 阻止循环引用：即将对象本身作为对象的某个属性值

- jQuery.each()：如果回调函数返回值 `=== false`，则会中止迭代

- 强制转换成字符串：`someVal + ''`

- 正则：`\s`（空白符）、BOM（\uFEFF）、NBSP（`\0xA0`或`&#160;`）都显示为空白，去除字符串首尾空白或字符串当中空白时要注意。
  原生的`trim()`方法对上面三种都有效，但是不明白jQuery 1.11.1源码中为什么加了`!trim.call("\uFEFF\xA0")`判断。

- Mi3 Android4.1.1 微信内置浏览器：input:search元素不能应用text-indent，输入焦点会向下偏移大约一行，
  而且输入框不能再次点击，必须在其下面位置点击才会出现光标，可输入文字，其他浏览器都正常。

  另外大部分浏览器对input中的text-indent处理有点小小的问题，在input刚获取焦点时，
  text-indent并未起作用，输入文字之后才会出现偏移量

##2014-12-28

- vim fold/unfold (折叠代码/展开代码):`:help fold`

  - **zo** Open one fold under the cursor.
  - **zc** Close one fold under the cursor.
  - **zR** Open all folds.
  - **zM** Close all folds.

- data-* 命名限制：

  - 不能以`xml`开头
  - 不能包含冒号（U+003A）
  - 不能包含大写字母 A-Z

- draggable 属性不是一个布尔属性，是一个枚举属性（enumerated attribute），因此，不要省略`true`，写全。

- id: 一个元素可以有多个不同的id属性

- title：

  - 对于值里面的换行符与空白符会保留，也就是可以多行
  - 继承最近的祖先元素的title属性设置，但是通过设置`title=""`可以用于覆盖，不显示

- `b` vs `strong`, `i` vs `em`: 在HTML5中，`b`与`i`也是语义标签，并未抛弃。关于它们的使用细节可以参考：

  - [http://stellify.net/html5-b-and-i-tags-are-going-to-be-useful-read-semantic-again/](http://stellify.net/html5-b-and-i-tags-are-going-to-be-useful-read-semantic-again/)
  - [http://www.w3.org/International/questions/qa-b-and-i-tags](http://www.w3.org/International/questions/qa-b-and-i-tags)

  上面的文章中建议使用`i`与`b`时加上`class`属性，可区分不同用途的`i`或`b`，从而依照不同用途控制样式。（好麻烦）

- 似乎任何事物都会随着时间改变，StackOverflow上面的问题随着标准或其他的改变可能不再正确，MDN上面的文档随着标准及浏览器的快速变化也可能跟不上，有些该抛弃的仍然存在，新的又可能来不及写文档。

##2014-12-29

- 标签页（Tab）、下拉菜单（Dropdown）等插件切换的可访问性：应该提供只用键盘就可以切换，比如`tab`键切换焦点，或焦点+空格键

- 一切插件的设计可以参考别人的思考，比如下面这些链接，不止这些，多搜索，从各方面考虑吧：

  - [http://www.smashingmagazine.com/2009/03/24/designing-drop-down-menus-examples-and-best-practices/](http://www.smashingmagazine.com/2009/03/24/designing-drop-down-menus-examples-and-best-practices/)
  - [http://www.smashingmagazine.com/2009/05/27/modal-windows-in-modern-web-design/](http://www.smashingmagazine.com/2009/05/27/modal-windows-in-modern-web-design/)
  - [http://www.smashingmagazine.com/2014/09/15/making-modal-windows-better-for-everyone/](http://www.smashingmagazine.com/2014/09/15/making-modal-windows-better-for-everyone/)

##2014-12-30

- 验证码验证状态：填写正确/填写错误/时间过期

##2014-12-31

- Fullscreen API：几乎所有浏览器都支持，F11（或其他快捷键）全屏切换，个人现在认为github在线编辑的全屏编辑模式就可以了，
  当然自定义视频播放器UI时，可能要利用JS来控制全屏

- Modal/Box等弹出层插件支持键盘操作：比如`ESC`关闭，`Enter`确认等

2015
====

##2015-01-05

- HTML entity(实体符号)：HTML5相比HTML4多增加了一些，另外有些多了一些别名。部分低版本的浏览器，如IE6，不支持新增的实体符号或别名

  - [http://dev.w3.org/html5/html-author/charref](http://dev.w3.org/html5/html-author/charref)
  - [http://www.w3.org/TR/html4/sgml/entities.html](http://www.w3.org/TR/html4/sgml/entities.html)

- label与表单元素：要想点击label使表单元素获取焦点，除了label包裹外，还要设置label的for属性为表单元素的id属性值

- `a`元素没有设置href属性时，在IE6中，点击时仍会重新加载当前页面

##2015-01-06

- 参数值为函数时，比如插件中常见的回调函数，默认为空函数呢？还是为`null`？如果为`null`，需要利用`&&`来判断一下，但是调用函数开销更大吧？

  jQuery插件可以使用jQuery.noop来节省字节。

- 假设一个动画开始时刻为t0，结束时刻为t1，持续时间为T（T可能为0），则常用的两种回调：`onbeforecallback`在t0时刻之前的一刹那执行，并且可以阻止动画的执行，`oncallback`在t1时刻之后马上执行，所以称之为`onaftercallback`也可以。

##2015-01-07

- 国际化中常见语言（可参考Google/Microsoft/Facebook/Twitter等）：有些网站是按地区来分的，就好像不同国家的英语会有区别，如英国、美国、印度等。

  - 英语
  - 法语
  - 西班牙语
  - 葡萄牙语
  - 荷兰语
  - 葡萄牙语
  - 阿拉伯语
  - 俄语
  - 日本语
  - 韩语
  - 中文（简体）
  - 中文（繁体）
  - 等等

  好像只有CJK不属于字母文字，其他常用的都是用有限的字母来表示，会用空格分割成单词。

- Zero-width space (ZWSP)：零宽空格是一个非打印字符，可以区分单词边界，其Unicode为`U+200B`，HTML实体符号为`&#8203`。参考[http://en.wikipedia.org/wiki/Zero-width_space](http://en.wikipedia.org/wiki/Zero-width_space)。

##2015-01-08

- 采用动态合并请求来处理js/css的合并；另外整体上就应该设计（拆分）好业务模块，而不是一个页面对应一个业务js/css文件，有些模块好多地方用，当然也包括CSS。

- 重构比新做还要难

- 重用代码，即可复用，使用函数或者模块化啊，而不是笨得去想着怎么快速复制代码文本。

##2015-01-09

- `<ul>`嵌套多层时，bullet样式在Chrome/Firefox/IE中表现一致，都是第一级为黑色小圆点，第二级为白色小圆点，第三级及以后都为黑色方框。

- jQuery的`prop()`/`data()`/`attr()`/`toggle()`/`toggleClass()`等方法经常遇到根据某个布尔值判断的情况，这时，可以不用`if`，直接将判断条件作为第二个参数，如：

  ```
  $('.list input:checkbox').prop('checked', flag === 1);
  ```

  或者判断条件比较长时，可以将其赋值给一个变量。

- 写一个方法，或者更常见的插件调用方法时，平衡参数控制与写死的常量，是否应该尽量使用变量控制呢？只要重复写过一次常量，就应该考虑使用变量？尤其对于显示相关的，如一段文本、颜色。

- `text-overflow`的浏览器支持：IE6+及其他所有主流浏览器，包括移动端都支持，所以也不许要`-webkit-`前缀了。

- 对于`display`属性值为`inline/inline-block`的元素，其宽度也会受到不同字体的影响。

- placeholder color:

  ```css
  ::-webkit-input-placeholder {
    color: red;
  }

  :-moz-placeholder { /* Firefox 18- */
    color: red;
  }

  ::-moz-placeholder { /* Firefox 19+ */
    color: red;
  }

  :-ms-input-placeholder {
    color: red;
  }
  ```
- alias 可以明显提高使用shell的效率，git也支持。

##2015-01-12

- Bash - 复制到并重命名一个文件：`cp httpd.conf{,.bak}`。这是因为`{}`符号，比如下面：

  ```bash
  echo {one,two,three}fish # Output onefish twofish threefish
  echo {"1 ","2 ","3 "}apple #Output 1 apple 2 apple 3 apple
  ```

  注意：逗号后面不能有空格。

- 脚本创建空文件方法：`touch test.txt` or `> test.txt`。

- vim: open a file and jump to end of file:`vi + tips.md`。

- 将Tab转换为空格（Convert tab to space）：

  ```bash
  expand -i -t 4 file.js > file1.js
  ```

  `-i`防止作为内容的tab被转换。

  批量转换：

  ```bash
  find . -name '*.js' ! -type d -exec bash -c 'expand -t 4 "$0" > /tmp/e && mv /tmp/e "$0"' {} \
  ```
  
  `/tmp`目录如果不与文件在同一个磁盘分区上时会导致速度很慢。

  Ref: [http://stackoverflow.com/questions/11094383/how-can-i-convert-tabs-to-spaces-in-every-file-of-a-directory](http://stackoverflow.com/questions/11094383/how-can-i-convert-tabs-to-spaces-in-every-file-of-a-directory)

##2015-01-13

- Vim - Cancel the search highlight:

  **Disabled the current search text:**

  ```
  :noh
  ```

  Or

  ```
  :nohlsearch
  ```

  **Disabled in current session:**

  ```
  :set nohlsearch
  ```

  Ref: [http://stackoverflow.com/questions/657447/vim-clear-last-search-highlighting](http://stackoverflow.com/questions/657447/vim-clear-last-search-highlighting)

- `Enter/Return`提交表单交互行为：触发当前获取焦点的表单元素的祖先`form`元素的`submit`事件。例如，`input:submit`或`button`元素的默认按`Enter/Return`键提交表单行为，必须要有个表单元素获取焦点才行；另外，如果`input:submit`或`button`不是任何`form`元素的后代元素也不行。


- 搞不懂`<option>`的`label`属性有什么用？[http://www.htmlhelp.com/reference/html40/forms/option.html](http://www.htmlhelp.com/reference/html40/forms/option.html)

- `<fieldset>`的`min-width`默认值为`min-content`，这会在某些浏览器中见到不符合期望的样式，如：[http://stackoverflow.com/questions/17408815/fieldset-resizes-wrong-appears-to-have-unremovable-min-width-min-content](http://stackoverflow.com/questions/17408815/fieldset-resizes-wrong-appears-to-have-unremovable-min-width-min-content)。

- `<progress>`标签的样式各浏览器都不一样，目前也很难用CSS精细控制，不好用，不如使用自己实现的，github有ProgressBar.js。

##2015-01-17

- 退格符`\b`，在大部分系统的控制台中输出时，都不会删除前面的字符，只是移动光标，然后变成了替换模式，换行符`\n`的作用是将控制台中的光标移动到下一行的开头。例如，C语言中`printf("hello worl\b\bd\n");`输出`hello wodl`，参考[http://stackoverflow.com/questions/6792812/the-backspace-escape-character-b-in-c-unexpected-behavior/6792867#6792867](http://stackoverflow.com/questions/6792812/the-backspace-escape-character-b-in-c-unexpected-behavior/6792867#6792867)。

- 控制台中发送/模拟文件结尾信号(EOF)：`Ctrl+D` (\*nix) or `Ctrl+Z` (Windows)

##2015-01-19

- input:checkbox 如果只写`checked`属性，而不是`checked="checked"`，则假设其对应元素为`check`，则`check.checked`值为`false`，而`check.getAttribute('checked')`值为空字符串。

- input:checkbox的indeterminate状态UI在不同系统不同浏览器中不同。其实HTML元素带有的默认的UI，绝大多数都是这样，跟随系统与浏览器，比如按钮、警告窗口。

- 脚本运行页面编码为GBK，脚本编码为UTF8，ajax(使用了$.when)获取UTF8编码的html文件，且请求头部设了UTF8编码，然后不知怎么至少IE9-都无法调用done。

##2015-01-20

- 旧代码中引入的全局jQuery对象与使用seajs引入的jQuery模块可能不是同一个，所以data()设置与获取不可以共享。

##2015-01-21

- 完成相同的功能，`innerHTML`、`appendChild`、`insertAdjacentHTML`效率对比：`appendChild`>`insertAdjacentHTML`>`innerHTML`。三者都兼容IE6+，但是`innerHTML`在IE6-9中对表格相关元素是只读的，`insertAdjacentHTML`在IE6中对表格相关元素无效。

##2015-01-22

- 跨浏览器获取选择文本：注意不同系统和浏览器对于跨“行”选择文本添加的换行符不同，比如Ubuntu Chromium下为`'\n'`，XP IE8中为`'r\n'`（还有可能包含一个空格）。

  ```js
  var text;
  if (window.getSelection) { // IE9+
    text = window.getSelection().toString();
  } else if (document.selection) { // IE6-8
    text = document.selection.createRange().text;
  }
  ```
## 2015-01-26

- 自己抛出异常错误时，带有文件名和文件行号的方法：

  ```js
  var __fileName__ = 'fileName';
  var __lineNumber__ = 0;

  throw new Error('some error', __fileName__, __lineNumber__);
  ```

  然后构建时使用工具将作为参数的`__fileName__`与`__lineNumber__`替换为正确的文件名与行号。（这样太麻烦了吧）

- 避免调用构造函数时忘记使用`new`关键字，可使用使用jshint检查，当然，有些插件或库的构造函数内部有一句代码来判断，比如underscore.js：

  ```js
  if (!(this instanceof _)) return new _(obj);
  ```

- 尽量避免判断内置对象的类型，在不同浏览器（主要是现代浏览器和IE8及以下不同），比如：

  ```js
  typeof document.getElementById; // 'object' in ie8
  
  var div = document.createElement('div');
  typeof div.offsetParent; // 'unknown' in IE8
  ```

  可以参考underscore如何检测对象类型。

- `Object.prototype.propertyIsEnumberable`：检测自有属性（owner property）是否可被遍历，与`for...in`或者说`in`不同。而`Object.keys`只返回可遍历的自有属性。

- IE6-8中，宿主对象(host objects)没有`hasOwnProperty`方法，可使用`Object.prototype.hasOwnProperty`，如`Object.prototype.hasOwnProperty.call(window, 'name');`。

##2015-01-29

- Nginx有可以限制每个IP的访问速率和并发连接数：Limit Requests和Limit Conn。后端应该了解Nginx的有哪些常用的模块，了解配置。为什么我看到了这个呢？因为公司的职位信息被别人不断地爬取。限制访问速率可以降低很多来自恶意IP的压力，但是很难彻底解决这种问题。要综合各种方法，提高别人恶意的成本。

- underscore的命名冲突解决方法是`var u = _.noConflict()`，源码：

  ```js
  var previousUnderscore = root._;

  _.noConflict = function() {
    root._ = previousUnderscore;
    return this;
  };
  ```

##2015-01-30

- 返回一个数值组成的数组当中的最大/最小元素：

  ```js
  var arr = [0, 3, 2, 8, 3];
  Math.max.apply(Math, arr); // 8
  Math.min.apply(Math, arr); // 0
  ```
##2015-02-02

- 在测试JS性能时，使用jsperf或者`new Date()`获取时间方式不一定可靠，发现只要先运行的函数运行慢，后运行的快，即运行顺序有影响，好比汽车加速一样。这是在测试underscore的`optimizeCb`函数发现的：[https://github.com/jashkenas/underscore/blob/95337a876784d5df7e62b2e55080ddf9199b6e9b/underscore.js#L63-L82](https://github.com/jashkenas/underscore/blob/95337a876784d5df7e62b2e55080ddf9199b6e9b/underscore.js#L63-L82)。

- underscore 2015-02-02的master分支中的`_.extend()`方法，会复制原型上的属性，只要`in`可迭代的。[https://github.com/jashkenas/underscore/issues/2023](https://github.com/jashkenas/underscore/issues/2023)

- 公司目前用的生成pdf文件的java包不是很好，比如需要严格的XML语法，在样式控制方面与浏览器有些不同，所以得单独针对打印版写些CSS。这个功能主要用来生成合同用的，每次管理部门都会使用Word设计一个合同样式，然后我们技术部门再更改，要不断调整样式，以接近Word的样式。

  改进：
  - 首先应该换一个可靠、兼容性更好的pdf生成库，如果没有好的java包，就换成其他语言的，比如js。
  - 弄一个所见即所得的在线编辑器供制作合同的人使用，不要使用Word设计。Word的在线渲染难度不知道大不大，反正Word功能繁杂。

- `String.prototype.slice`、`Array.prototype.indexOf`等函数的一些参数可取负值，其有效规则描述（以`Array.prototype.indexOf`的第二个参数`fromIndex`为例）：

  ```js
  startIndex = fromIndex >= 0 ? Math.min(array.length, fromIndex) : fromIndex < -array.length ? 0 : array.length + fromIndex；
  ```

- 求两个数的最大值或最小值：

  - 最大值：`max(a,b) = 1/2(a + b + |a - b|)`
  - 最小值：`min(a,b) = 1/2(a + b - |a - b|)`

- 绝对值：`abs(x) = sqrt(x*x)`

  `var abs(x) = function(x) { return x < 0 ? -x : x; }`

##2015-02-03

- Tab组件：hover交互效果，slide动画效果的组合，有些缺点：

  - 相对其他效果比较复杂，费时间
  - 如果能使用`transfrom`性能可以，但是IE9-的采用`margin`或定位方式都会触发重新布局
  - 不好解决的缺陷：比如鼠标从title1滑动到title3，即使采用延迟策略但还是触发了title2对应的动画，然后title2对应动画未结束就要开始title3对应动画效果了，这样会出现重叠现象。当然，可以采用探测鼠标移动速度来决定是否触发，这就更复杂，性能更低了。而click交互可以最大可能避免这种缺陷。

  **注**：上面的slide动画效果与另一种scroll组件的slide有区别，就是只会移动相关的两个项目，而不是移动整个容器。

##2015-02-04

- ie6-8的filter来设置半透明背景时，假设效果是：鼠标悬停时半透明背景元素出现，离开时元素隐藏。必须在`:hover`/`.hover`时设置半透明背景，离开时设置`filter:none;`。

- ie6：假设有HTML结构`<a><span>button</span></a>`，则要设置`a{text-decoration:none;}`才可以消除可能出现在`span`元素的文字下划线。

- `\9;`css hack同样对IE9和IE10也有效，对IE11无效（网上查询得知）。

  IE css hacks：

  - IE6 `_width: 100px;`
  - IE6-7 `*width: 100px;`
  - IE9+ `:root width: 120px;`

  可靠的方式还是采用条件注释式html标签class。

   各类hack集合: [browserhacks.com](http://browserhacks.com);

- 边框默认颜色（default border-color）是当前元素的文本颜色，而`<a>`标签默认未访问颜色为蓝色，已访问颜色为紫色，其默认边框颜色跟随文本颜色变化。

##2015-02-05

- 对`body`元素设置`background-color`，即使没写`body`元素，也会看到背景色，因为HTML parser会自动添加`body`元素。如果根元素`html`的背景色为`transparent`，且背景图为`none`，则**画布(canvas)**背景色设置为`body`元素的背景色。

- IE8-9浏览器对于`background-color`计算值为`transparent`的元素，click事件穿透该元素，触发其下面元素的click事件（注意：在点击区域，这个元素没有内容，其下面元素也要有内容或非`transparent`背景色）。

##2015-02-25

- 远程服务器上传递（上传/下载）文件，可使用SFTP，基于SSH的FTP，使用下面一行命令建立SFTP控制台会话：

  ```bash
  sftp username@ipaddress
  ```

  `help`命令查看帮助。

- Mac OSX上面，VirtualBox识别USB接口设备（如U盘）步骤：

  - 确保虚拟机系统处于关闭状态。
  - 对应虚拟机的Settings->Ports->USB里面，选择"Enable USB 2.0 (EHCI) Controller"，此时会提示安装VirtualBox扩展包，可到官网下载，需要注意扩展包版本号不能大于VirtualBox版本号。
  - 安装位置在Preferences->Extensions里面。
  - U盘先让主系统（Mac OSX）识别，然后虚拟机系统启动后，需要Eject U盘，虚拟机系统才会自动识别U盘

##2015-02-26

- 对于一个“布尔交互”（比如是否收藏商品），其中一个状态（false）可被设为默认状态，不需要传递数据，只需要请求某个链接（Restful）就行。其实只要是有限状态操作都可以。这是从Stackoverflow的收藏功能知道的，为了省流量，也是蛮拼的。

- JS/CSS的Source Maps只要用来调试生产环境中的压缩/合并/编译代码的，可以将生产环境中的代码转换成原始的开发版代码。对于那些没有设置开发模式（比如通过参数可以加载开发版代码）的网站比较有用，而且只在打开调试工具时才加载Source maps，所以不会增加请求数。当然个人认为，还是提供了开发/调试模式接口的配置更好些。

  可以使用Uglify生成

  参考：http://www.html5rocks.com/en/tutorials/developertools/sourcemaps/

- 像日期对象的`toString()`方法一样，可以覆盖自定义对象的`toString()`方法，比如一个Color对象，可以使其`toString()`方法输出RGB字符串，可以接受颜色模型参数。不仅仅时`toString()`方法，其他方法属性也可以，比如`toJSON()`、`valueOf()`。

- `null`/`undefined`转成数字：

  ```js
  +null; // 0
  +undefined; // NaN
  Number(null); // 0
  Number(undefined); // NaN
  Number(); // 0
  ```

##2015-02-27

- 变量/函数名等命名习惯，读更多别人的代码，了解命名习惯，有些人起得名字就是怪怪的。一半单词缩写时，取前三个或四个字母，或者取辅音字母。

- Font Awesome字体的HTML entities：[http://fortawesome.github.io/Font-Awesome/cheatsheet/](http://fortawesome.github.io/Font-Awesome/cheatsheet/)

  由于IE6-7不支持`:before`/`:after`/`content`，所以可以使用HTML实体符号，另外也是图片也是官方提供的一种回退方案。

- jQuery删除某个css属性：`$(selector).css(property, emptyString)`。

- `opacity`小于1时，会创建新的Stacking Context，效果似乎是同正常的文档流中出现的顺序，`z-index`的值无影响，除非利用定位创建Stacking Context。

  参考：
  - http://philipwalton.com/articles/what-no-one-told-you-about-z-index/
  - http://jsfiddle.net/4L3hgwjg/1/

- 在Ubuntu 14.04 LTS/Chromium 39中，当光标焦点在`textarea`中时，按`ESC`键，第一次会使`textarea`失去焦点，且不`textarea`和`document`的`keyup`事件。当其失去焦点时，且没有任何元素获取焦点时，默认焦点元素为`document`，此时按`ESC`会触发`document`的`keyup`事件。所以导致在github的zen code mode中需要按两次`ESC`才能退出全屏模式。

  而Firefox及Windows上面的浏览器不会这样。

- Font awesome的字体图标在XP的IE6中显示比较模糊，在未开启ClearType的情况下，非常模糊，都变形了，当然，其他宋体什么也是模糊。

##2015-02-28

- IE9中，删除内容交互时，`onpropertychange`与`input`事件不会触发。其中，删除交互包括退格键/Delete键/Ctrl+X/右键菜单的剪切和删除选项。实际应用中可以只探测退格键，比如统计输入字数时。参考：http://help.dottoro.com/ljhxklln.php和http://help.dottoro.com/ljufknus.php。

- 至少IE9中，当`textarea`中有内容时，按`ESC`键会删除部分或全部内容。

- IE6中，在设置`body`高度为`$(window).height()`，且设置了`overflow: hidden;`属性后，可能会出现滚动条，可以设置`html`。

- 假设一个`div`有以下样式：

  ```css
  div {
    width: 300px;
    white-space: pre-wrap;
    _white-space: pre;
    overflow: hidden;
  }
  ```

  其内容以Windows下的`\r\n`字符作为行结尾符时，则在IE8中，会有空行，在IE6换行，但是似乎把`\n`显示成了空格的效果。如果行结尾符是`\n`，则IE6中不会换行，但似乎显示为空格效果，IE8会换行，不会有空行。

##2015-03-02

- MDN上有些DOM文档质量有待改善，help.dottoro.com值得参考，比如`scrollHeight`与`change`事件文档。

- 假设一个`textarea`被设置以下样式，假设`rows=1`：

  ```css
  textarea {
    font-size: 14px;
    line-height: 20px;
    overflow: hidden;
  }
  ```

  在IE6/7/8中，`textarea`的实际`height`是18px，`line-height`不会影响显示高度，但是会影响`scrollHeight`，即内容的滚动高度，此时为28px(IE7/8)/26px(IE6/9)，因为默认的`padding`不同。注：`scrollHeight`包含内边距。实际使用中，最好明确设置`height`值为`line-height`的N倍。

##2015-03-03

- IE6/7或文档模式为IE7时，`clientWidth`/`clientHeight`对于`hasLayout`为`false`的元素，值为0。

- slider，即图片轮播组件，只有一张图片的处理。

- 似乎只有`keyup`事件才会在中文输入法状态下返回期望的`event.keyCode`值。

- 单选/多选按钮的文字与按钮关联，单击文字都起作用，时刻注意这个问题吧。

##2015-03-04

- 一个很奇怪的问题，在做一个检测到input里面输入的字符为中英文分号功能（onproperty or input事件），在IE6里，使用搜狗或QQ输入法输入中文分号时，IE6说mshtml.dll加载项遇到故障，然后浏览器就会关闭，使用智能ABC没问题。最后退而求其次，使用`keyup`事件解决了。

- `ime-mode`属性可以控制输入法是否激活，只有Firefox与IE5+支持。

- Ubuntu 14.04/Firefox 36中，`textarea`中被选择的内容会自动删除，无论通过快捷键选择，还是鼠标，还是菜单。

- 快速定位文件及方法：可以在本地配置在页面输出页面对应的JSP文件本地路径，包括Include进来的，输出到注释里，可快速定位JSP文件。所有的页面JS文件入口也尽量只有一个，放在页面底部

##2015-03-05

- 默认不设宽度的`textarea`，设定了`overflow:hidden`，当内容增加到一定高度，设置`overflowY:auto`出现滚动条时，IE8中滚动条会增加宽度，而Chrome中就不会，浏览器不一致，可以显式设定宽度。

##2015-03-06

- `textarea:hover`选择器似乎不起作用，在Chrome中也是，可以外面套一层`div`，或者通过`mouseover`事件。

##2015-03-07

- Sublime正则替换捕获组使用`${1}`表示第一个。

- `rm`删除连接符`-`开头的文件名：

  ```bash
  rm -- -abc.txt
  ```

##2015-03-08

- github仓库语言统计标记主语言错误，使用.gitattributes配置，查看：
  https://github.com/github/linguist#troubleshooting

##2015-03-09

- 截止目前，`element.dataset`的读写速度比`element.getAttribute()/element.setAttribute()`慢，只有几分之一。

##2015-03-11

- jQuery performance: Caching jQuery selector and chaining jQuery manipulation

  https://jsperf.com/caching-jquery-selectors/15

##2015-03-12

- 有几个常见的网站小功能，很多网站的体验很差：

  - 下拉自动加载：感觉这个自动不适合PC端，或者加载几页之后变成手动点击才能加载，或者确定自动加载的内容下面不要出现任何其他内容了，比如页脚的导航。
  - 加载更多：无论时自动加载，还是手动点击，还是只有“前一项”和“后一项”的分页导航，采用AJAX时，刷新之后只显示第一页内容的体验不太好。
  - 回到顶部：以前和同事争论时，自己当时倾向有动画效果，不过后来觉得没有动画效果或者动画效果时间短一些比较好，没做过测试，不了解其他人怎么想的。
  - 移动站底部广告条：常见于推广自家APP，其中的那个用来关闭的叉按钮，显示虽然只有叉，但是应该将实际的范围扩大到右侧的一小块矩型区域，要不然容易误点。
  - 用JS做跳转页面功能：个人觉得主要的内容（比如文章列表的每篇文章的链接）的链接用JS跳转不合适，移动端网站上常常是点击一个区域都可以跳转或打开一个新标签，但是这个区域用`<a>`标签包裹，这也是符合HTML5规范的，这样在Chrome下长按可以选择在新标签页打开，不依赖JS。典型反例就是拉勾网，一是加载更多刷新之后只显示“第一页”，二是跳转功能用JS实现的，导致Chrome浏览时查看一个职位详情之后返回就又变成刚进入网站时显示的内容，还要再点击加载更多好几次，又不能在新标签页打开，还有就是没有用户反馈的地方，难用的很。

##2015-03-17

- 在Chrome中（至少今天以前）如果祖先元素应用了`transform`之类的CSS属性，那么该元素固定定位的相对点变成了应用`transform`的祖先元素。参考：http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/和http://stackoverflow.com/questions/2637058/positions-fixed-doesnt-work-when-using-webkit-transform

##2015-03-19

- NPM一次安装多个包：

  ```bash
  npm install gulp-jshint gulp-uglify --save-dev
  ```

##2015-03-20

- Mutation events(deprecated)的DOMAttrModified事件只能由脚本触发，用户交互不会触发。Webkit系浏览器不支持该事件。

##2015-03-22

- AltGr (also Alt Graph) key：用来输入特殊字符，比如`∑øπ`等，PC键盘上为右边的`Alt`键，OSX键盘上为任一`Option`键。参看维基：[http://en.wikipedia.org/wiki/AltGr_key](http://en.wikipedia.org/wiki/AltGr_key)。

- mousedown的默认事件包括选择文本操作，可以通过`Event.preventDefault()`阻止文本选择，IE9+才支持，

##2015-03-24

- `Node.contains` and `Node.compareDocumentPosition`：

  ```js
  // http://ejohn.org/blog/comparing-document-position/
  function contains(a, b) {
    return a.contains ?
      a.contains(b) :
      !!(a.compareDocumentPosition(b) & 16) || a === b;
  }  
  ```

  注意：IE6-11中，`document`没有`contains`方法

- Composition events：`compositionstart`/`compositionupdate`/`compositionend`三个事件，IE9+支持，但是三个事件在不同浏览器，或者不同系统平台的相同浏览器上表现不一致：

 - IE9/Win7：测试最符合规范
 - IE10：有说
 - Chromium41/Ubuntu：`compositionupdate`在选择字符序列后不触发，直接触发的`compositionend`的`event.data`为空字符串，不对。
 - Firefox36/Ubuntu：在获取焦点时竟然也会触发。
 - IE10：没亲测，别人文章说在`compositionstart`之后只触发一次`compositionupdate`，然后就不触发了。
 
  总之有不少问题，可能不同的输入法都不同，这么多系统平台/浏览器/输入法，不一致的细节太多。可能相同点就是会触发这三个事件。

  Ref:

  - http://blog.evanyou.me/2014/01/03/composition-event/
  - http://www.cnblogs.com/chyingp/p/3599641.html?utm_source=tuicool

- 禁止选择文本：兼容IE6+及其他现代浏览器。首先利用CSS属性`user-select`，不支持的使用`selectstart`事件的阻止默认动作`preventDefault()`/`returnValue=false`。对于IE/Opera，`unselectable`虽然可以，但是不能继承，只能对单个元素的文本内容有效。

- `select`事件只能绑定在`input`/`textarea`元素上才有效。

##2015-03-25

- Focus Event Order：Chrome与标准不一样，focus->focusin或blur->focusout，而IE与标准一样

- focusin/focusout：`FocusEvent.relatedTarget`，IE9+支持，IE6-9使用`e.fromElement`

- Firefox36/Ubuntu14.04中，`<button>`元素的子元素`<span>`绑定的`click`事件没有触发。

- 同一个元素，相同事件（支持捕获与冒泡）绑定两个事件监听器：一个冒泡阶段，一个捕获阶段。如果该元素处于**Target Phase**，则先绑定的事件监听器先执行。

##2015-03-26

- IE6-7（只测了IE7 simulated IE8)中使用`delete`删除宿主对象的属性会报错“对象不支持此操作“，比如：

  ```js
  var document.body.data123 = 22;
  delete document.body.data123;
  // 抛出异常”对象不支持此操作“
  ```

  可以使用`try...catch`捕获异常，对于元素对象可以使用`removeAttribute`删除元素属性。

##2015-03-27

- 脚本触发一个事件，要考虑三个问题：

  - 调用事件对应的所有监听器
  - 事件的冒泡与阻止冒泡
  - 事件的默认行为

- 业务规则与极端情况：一次获取公司发布过的职位信息（包括已停止招聘和已删除的），从而导致数据量最大可超过1M，而且随着时间积累会越来越多。数据是通过AJAX请求的，超时限制是10s，对于当前大部分人来说10s下载1M不成问题，但是网络连接差的或者数据量更大的会导致超时。个人认为要么改变业务规则，要么就要针对极端情况特殊处理了。

##2015-03-30

- 检测大部分DOM2事件可是使用这篇文章的方法：[http://perfectionkills.com/detecting-event-support-without-browser-sniffing/](http://perfectionkills.com/detecting-event-support-without-browser-sniffing/)，然后`focusin`/`focusout`事件在IE中可使用这种方法检测，Chrome/Firefox中不行，可使用一种不优雅的方法：[http://stackoverflow.com/questions/7337670/how-to-detect-focusin-support#answer-7337873](http://stackoverflow.com/questions/7337670/how-to-detect-focusin-support#answer-7337873)。

- URL Hash通常可以跳到/滚动到页面内的某部分内容，但是这部分内容要是Ajax加载的就不会跳了，得手动控制。

- 公司有一个`util.js`文件，感觉集合了太多功能模块，里面包含了：localStorage、History、alert、表单验证、underscore模板解析、URL解析、浏览器探测、遮罩层、loading层、键盘事件绑定、不完善的`input`事件等，虽然4000行代码不多，但在更改某个功能或者再想移除某个功能成单独模块就难了，所以一开始就应该细颗粒化。

##2015-03-31

- `Event`/`MouseEvent`等事件对象的属性（非自定义）是只读的，在IE8中给事件对象赋值会报错“找不到成员”，在Chrome41中会静默失败，所以jQuery才自己创建一个事件对象。

##2015-04-03

- 修正一个弹窗内容在安卓平板上不显示问题（该页面及弹窗都是针对PC版的），另外在安卓自带浏览器及UC上也不显式，在Chrome中显示。问题原因是左浮动的元素没有设置宽度，且设置了`overflow-x:hidden`。但是我调试了一个半小时才找到原因，这也是由于国产浏览器难于调试，我只会`alert`，最终还是桌面上看CSS设置分析出来的，我的调试过程：

  - 一开始我怀疑是DOM片段没有加载或者没有插入到文档中;
  - 我用`alert`不断插入分析，手机输入用户名密码麻烦，而且我没有修改加载JS的路径配置，导致加载线上版本，我一直怀疑没清除干净缓存;
  - 发现都没问题，然后这才怀疑CSS的问题，先是定位，然后是高度，然后竟然怀疑字体颜色问题；
  - 最后通过Chrome在桌面分析样式发现设置了浮动，但是没设置宽度，才分析到点上。


##2015-04-07

- 无法获取本地存储数据----调试本地存储写入问题----发现搜索框值返回不正常----问后端人员是resin3没有配置编码问题，GBK相关

##2015-04-08

- Resin有个配置没配置好导致将Ajax请求的utf8编码的静态html强制转换为gbk，导致了乱码问题，使用nginx代理就好了。应该是配置问题。

- 为什么ajax请求的utf8编码的中文内容（没有ASCII编码）显示在GBK页面时不乱码呢？

- 无法获取焦点的元素，比如`div`，与键盘事件什么关系呢？

##2015-04-09

- `new RegExp`创建正则时注意字符串的特殊字符的转义。

- jquery animate scrollTop: `$('html, body').animate({scrollTop: 0}, 400)`

##2015-04-10

- IE8中，`setTimeout`触发的时刻误差很大，比如`for`循环设置每 20ms 触发，共 300ms，结果变成了 20ms, 70ms, 120ms之类，大约持续时间 600ms 左右。不过可以根据当前已经过的时间来设置要改变的动画参数。


