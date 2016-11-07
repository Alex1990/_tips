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
 - Chromium41/Ubuntu：`compositionupdate`在选择字符序列后不触发，直接触发的`compositionend`的`event.data`为空字符串，不对。
 - Firefox36/Ubuntu：只有鼠标点击就会一直触发，也会触发`input`事件。
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

- sed replace multiple files on OSX: `sed -i -E 's/\(foo\)\(123\)/\1 \2/g' *.txt`

- 脚本触发一个事件，要考虑三个问题：

  - 调用事件对应的所有监听器
  - 事件的冒泡与阻止冒泡
  - 事件的默认行为

- 业务规则与极端情况：一次获取公司发布过的职位信息（包括已停止招聘和已删除的），从而导致数据量最大可超过1M，而且随着时间积累会越来越多。数据是通过AJAX请求的，超时限制是10s，对于当前大部分人来说10s下载1M不成问题，但是网络连接差的或者数据量更大的会导致超时。个人认为要么改变业务规则，要么就要针对极端情况特殊处理了。

##2015-03-30

- Markdown输出反引号：双反引号，且与作为内容的反引号间隔一个空格，如`` `` ` `` ```

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

##2015-04-11

- 当处于浏览器隐私模式时，`localStorage`在某些浏览器中虽然可用，但是会抛出错误，比如 Safari 8.0.3中。

- 有一种观点：Cookie是用来向服务端提供数据的，而localStorage仅向客户端提供数据。

##2015-04-12

- IE6/7的 UserData 每个路径都有大小限制，不过可以通过隐藏 iframe 来实现整站的，突破路径限制，但是每个域名也有限制：640kb-1024kb，每个路径限制是 64kb-128kb。可参考：https://github.com/mattpowell/localstorageshim。

- `Date`对象有大小限制，也即表示的时间有范围，1970-01-01 00:00:00 两边各 8.64E15 毫秒，超过这个范围会变成 "Invalid Date"。规范：http://ecma-international.org/ecma-262/5.1/#sec-15.9.1.1


##2015-04-13

- 调试定位分析，测试发现IE7中出现错误，仅仅根据这个信息无法得出就是IE7浏览器的bug，我就直接以为是 IE7 的问题，自己测的 IE6、IE8模拟IE7、IE8中都没有问题，都是连接的测试服务器，然后我就认为必须安装一个 IE7，没办法安装，试了很多下载包、方法。然后才想着用测试人员的虚拟机测了一下本地的和 svn 的，发现没问题，这才觉得可能并不是 IE7问题。然后想着是不是测试服务器与svn/本地有文件不同，用自己的 Chrome 比对了我提交的几个js文件，发现都一样。然后明天继续，怀疑不能简单的Ctrl+R来清缓存，又或者Cookie之类关系。

##2015-04-14

- 有用绝对定位的`label`/`span`标签实现输入框的`placeholder`属性，如果该标签的在输入框的上面，可能 click 事件时，输入框事件 blur --> focus，这样对其他依赖 focus/blur 切换的组件产生影响，可以通过设置样式将标签放到输入框下面，但是输入框颜色必须透明。

##2015-04-15

- 回到顶部（Back to top）注意事项：

  - 用户浏览器宽度范围，比如PC端通常1024px~1920px，尤其注意1024时如何显示
  - 滚动动画效果，是否有动画？动画时长？easing function?
  - 固定定位设置，相对主内容还是浏览器边框？相对主内容得用脚本来设置位置
  - z-index的设置
  - 兼容IE6的话通过JS设置，expression相当耗性能

##2015-04-17

- vim 设置了`expandtab`，输入 Tab 字符方式：`<C-V>Tab`

##2015-04-20

- 设置多个inline style时，使用`elem.style.cssText`属性只会触发一次reflow与repaint。

##2015-04-21

- 不同触屏设备的分辨率大小及像素比不同，使用像素图做图标在放大时会失真。

- UC浏览器会识别页面底部的推广APP的广告条，然后屏蔽掉，使用IP访问可以显示。

- UC浏览器开发者中心：[http://www.uc.cn/business/developer/](http://www.uc.cn/business/developer/)，包括UC浏览器开发者版、User-Agent使用说明书、U3内核扩展接口、开放参数数据字典等。

- `localStorage`在有些（手机）浏览器的隐私模式/无痕浏览中可以使用，有些浏览器中不可以使用，使用`localStorage`之前必须检测是否可用，`typeof localStorage === 'undefined'`不可靠，可以使用：

  ```js
  try {
    var ls = window.localStorage;
    // while in private browsing mode, some browsers make
    // localStorage available, but throw an error when used
    ls.setItem('~~~', '!');
    ls.removeItem('~~~');
  } catch (err) {
    ls = null;
  }
  ```

  使用 Cookie 记录简单的数据，比如 1KB以内，甚至可以只用 Cookie 记录一个编号（如果用户没登录，可能涉及到如何唯一标记浏览器），服务端记录具体的数据。
可以设置 Cookie 的 path，甚至将 Cookie 限制到一个 path 中，然后利用隐藏iframe技术读取。

##2015-04-22

- grunt-contrib-cssmin 在压缩类似下面样式时，默认配置下，层叠会出现错误：

  ```css
  .input {
    padding: 10px 5px 5px;
    border: none;
    border-bottom: 1px solid #ddd;
  }
  .submit {
    padding: 0;
    border: none;
    border-bottom: 1px solid #ddd;
  }
  ```

  可以配置`advanced: false`来关闭选择器属性合并，详细可查看 clean-css 的参数说明。

- `@font-face`在 Virtualbox + XP + IE8 里面初次加载页面是可能不显示，需要鼠标移上去才显示。

## 2015-04-23

- 对于要加载很多资源的功能/模块，在正在加载时给出反馈信息，比如用个带有loading图的遮罩层，而不是当用户点击时告诉用户“正在加载中”，当长时间（30s以上）加载未完成时给出提示，像 Gmail 那样。

## 2015-04-27

- `mousedown`, `mouseup`, `click`, `blur`事件触发（非脚本）顺序：`mousedown`-->`blur`-->`mouseup`-->`click`。

- Chrome 41支持`selectionchange`事件了，不知道确切什么时候开始支持的。不过以前用`selectionchange`来补救 IE9 中`input`事件的bug的判断条件写错了，不能通过`addEventListener`与`selectionchange`来判断是否是 IE9，可以使用`attachEvent`与`addEventListener`与`userAgent`，再结合`selectionchange`来判断。

  注：以前的 Opera 也支持 `attachEvent`，可能是未采用 Blink 之前的，虽然份额非常非常低了。

## 2015-04-28

- 不要使用`self`代替`this`，因为`self`是个全局变量，指向当前`window`，可以使用`_this`/`that`之类。另外这种方式比 jQuery.proxy, Native bind, Underscore.js bind 之类的方法性能高好几倍：http://jsperf.com/bind-vs-jquery-proxy/5。

## 2015-04-29

- 有些问题不是布尔问题，而是 hash 问题，只不过特例正好是布尔问题，就不要使用布尔判断了，没扩展性。比如第三方登录有很多平台，刚开始正好只有两个：微博和QQ，于是使用`var title = loginType === 0 ? "微博登录" : "QQ登录";`，不久之后要添加微信登录，甚至还会添加其他登录，这代码还得重写。

## 2015-05-05

- Boolean paramters are wrong 一文，说布尔参数没有语义，代码可读性不好，可使用一个变量代替，参考链接：http://blog.ometer.com/2011/01/20/boolean-parameters-are-wrong/。

## 2015-05-07

- script tag 加载方式，有人说其中一个缺点是全局变量冲突，全局对象上的变量多，但是这只是一个规定就可以解决的，大不了都暴露在一个全局变量上，命名也可以利用文件系统的天然防冲突策略。

  也有说 script tag 加载方式需要重复写多个`<script>`标签，但是也可以写个工具函数来自动插入啊。

  总之我想说的是，AMD/CMD 加载方式只是解决了常规的 script 方式的缺点，但是本质上难道还不是 script 加载方式，只是概念的定义问题。

## 2015-05-09

- `Storage`对象相关的`storage`事件触发条件：

  - 在相同域名下的其他页面发生的改变，比如另一个标签页或`iframe`
  - 改变是指域名对应的`Storage`对象发生改变，即设置、更新或删除数据项时，重复设置相同的键值不会触发该事件，`Storage.clear()`方法至多触发一次该事件。

  注意：`sessionStorage`对象，即时相同域名，但是不同页面（新窗口或新标签页）对应不同的`sessionStorage`对象。

  `storage`事件对应的事件对象包含额外的几个属性：

  - storageArea：发生改变的存储对象
  - url：发生改变的页面URL
  - key：发生改变的数据项的键，不存在则为`null`（Ch41/FF37）或空字符串（Sf8）
  - oldValue：发生改变前的旧值，不存在则为`null`
  - newValue：发生改变后的新值，不存在则为`null`

## 2015-05-11

- Chrome 41：未开启 strict mode 时，`const`无效，`let`导致抛出错误。

  **const**: 严格模式下，声明时必须被初始化，赋值会导致抛出`TypeError`异常，如果常量是个对象，则可以添加/修改/删除属性。

- How do you pronounce "Mozilla"?

  **Mo** as in Mosaic - **zilla** as in godzilla.

- Terminal 中，`echo $(someVar)`会将换行符替换为空格，且删除末尾换行符。

  http://stackoverflow.com/questions/8467424/echo-new-line-in-bash-prints-literal-n

## 2015-05-18

- 浏览器控制台下可使用`monitorEvents(Element)`来监听一个元素发生的所有事件，调式很好用。

- 关闭 iPhone/iPad Safari 的自动大写首字母和自动纠正拼写功能：
  `autocapitalize="off"`和`autocorrect="off"`。

- 关于`autocomplete`属性的一个讨论：http://security.stackexchange.com/questions/49326/should-websites-be-allowed-to-disable-autocomplete-on-forms-or-fields。一些人使用标签（如`label`）来模拟`placeholder`属性，同时`autocomplete`功能开启时（默认），则鼠标移动到浏览器的自动完成列表选项上时，输入框的文本可能与`placeholder`的标签文本重合，而且目前没有相应的事件来监听，所以通常除了用户名与密码外关闭`autocomplete`功能，或者`placeholder`可以使用 [juery-placeholder](https://github.com/mathiasbynens/jquery-placeholder)。另外，验证码这种一次有效的绝对不能用`autocomplete`功能。

  `autocomplate`属性值与输入框的`name`属性关联：https://html.spec.whatwg.org/multipage/forms.html#autofill

- `transition`与`jQuery.animate()`一起用时，可能得不到期望的结果。

- `Element.oneventname` 方式绑定的事件，在 IE6-8 下事件回调函数第一个参数不是事件对象，而`attachEvent`绑定的方式，第一个参数是事件对象。（一直记错了）

## 2015-05-19

- Companion.JS 用来调试 IE6 JavaScript。

- 使用浏览器探测技术的来决定功能时不可靠，比如依赖 IE >=9 支持`addEventListener`，但是浏览器模式为 IE9+，文档模式低于 IE9（这种情况在 360 浏览器可能发生）时，会发生错误。对于 IE 可使用 'document.documentMode'（该属性仅IE支持）来判断也比`navigator.userAgent`更可靠。

  对于 360 使用极速模式：

  ```html
  <meta name="renderer" content="webkit">
  ```

  使用最高版本文档模式：
  ```html
  <meta http-equiv="X-UA-Compatible" content="IE=Edge">
  ```
## 2015-05-21

- 判断 一个数值是正0，还是负0：`n === 0 && 1 / x === Infinity`和`n === 0 && 1 / x === -Infinity`。

## 2015-05-25

- 不能使用`Date.parse()`来判断一个值是否是日期字符串，因为当一串数字作为字符串尾字符串（忽略空白符）时，仍会被该方法解析，如`Date.parse('foo 42 ')`。

- `navigator.cookieEnabled`不可靠，在某些 IE9 中，即使禁用了 Cookie，该属性仍然返回`true`，我自己虚拟机Win7/IE9测试没问题，更可靠的检测方法：

  ```js
  var enabled = ("cookie" in document && (document.cookie.length > 0 ||
        (document.cookie = "test").indexOf.call(document.cookie, "test") > -1));
  ```

  参考：[http://stackoverflow.com/questions/6125330/javascript-navigator-cookieenabled-browser-compatibility](http://stackoverflow.com/questions/6125330/javascript-navigator-cookieenabled-browser-compatibility)

- Sublime 添加新的 Snippet，如果不起作用，可能是`<scope></scope>`值不在`"auto_complete_selector"`设置中。

- 对于默认不可获取焦点的元素（如`div`、`li`），通过设置`tabindex`可以使其变为可获取焦点的元素。`tabindex`为负值时，无法通过`Tab`键使其获取焦点，为`0`时按元素在文档流中的顺序跳转，这也是其他可获取焦点元素的默认行为。而`HTMLElement.focus()`/`HTMLElement.blur()`方法可以控制一个元素是否处于获取焦点状态。

## 2015-05-26

- 通过一个对象（Hashmap）来映射方法调用，如：[http://jsperf.com/date-methods-hash](http://jsperf.com/date-methods-hash)。但是经过 Gzip 压缩之后大小几乎一样，但是性能确低一点点儿。所以还是根据具体情况考虑。

- `new Date(dateString)`/`Date.parse(dateString)`中的`dateString`的最后是表示时区（Time zone），例如：`"Tue May 26 2015 10:21:13 GMT+0800 (CST)"`、`"Tue, 26 May 2015 02:21:55 GMT"`。有时相同的缩写表示不同的市区，缩写列表见：[List of time zone abbreviations](http://en.wikipedia.org/wiki/List_of_time_zone_abbreviations)。

- `toUTCString()`在 Chrome 和 IE（至少6-9）中的返回值不同，Chrome 的时区缩写是`GMT`，IE 中是`UTC`。`toGMTString()`（被废弃）与`toUTCString()`返回值一样。

- Cookie max-age 与 Cookie expires 区别：HTTP 1.1 已经抛弃了不易用的`expires`；几乎所有（IE6+）的浏览器都支持`expires`，而 IE6-8 不支持`max-age`，而支持`max-age`的会优先使用`max-age`。

- 正则`/\s/`在IE9+及其他现代浏览器中等价于：`[ \f\n\r\t\v\u00a0\u1680\u180e\u2000\u2001\u2002\u2003\u2004\u2005\u2006\u2007\u2008\u2009\u200a\u2028\u2029\u202f\u205f\u3000]`，而在IE6-8中等价于`[ \f\n\r\t\v]`。

## 2015-05-29

- `blur`与`change`事件顺序：`change`>>>`blur`。两个事件不一定一起发生，比如`select`选择时，发生`change`但不发生`blur`；又比如`a`元素发生`blur`确不发生`change`。

## 2015-05-31

- 注意：不同的域名或路径可以设置相同名称的 Cookie，因此把 Cookie 解析成对象时可能会有问题，当然这种情况实际应用中也不太可能出现。也没有完美的使用 JavaScript 删除 Cookie 的方法。参考：http://stackoverflow.com/questions/595228/how-can-i-delete-all-cookies-with-javascript。

## 2015-06-01

- 比较两个对象是否相等（非严格，比较属性相等就行，deeply），可以使用 Underscore/LoDash 的 `_.isEqual` 方法。

- 更宽松的验证？比如固定电话（区号/电话号码/分机号）只要数字就行。这与比较严格的验证（比如，区号3/4位、电话号码7/8位）相比优劣呢？

## 2015-06-02

- jQuery.serialize() 会将空格替换为`+`。

- `label`标签与一个表单元素关联时，如果点击(`mousedown`）`label`的默认行为是让表单元素获取焦点，但是仍然会触发改表单元素的`blur`事件（假设该表单元素处于获取焦点状态）。

## 2015-06-03

- 点击`<a href="javascript:void(0)">Reload</a>`刷新验证码，应该阻止`a`标签的`click`事件的默认行为，否则 IE6 下面`img`的`src`属性会去掉，不显示图片。

## 2015-06-04

- 使用`input.type`来改变`type`属性时，在IE6-8（包括改变文档模式来模拟的）中会报错；使用`input.setAttribute()`时，在IE6/IE8/IE7(in IE8)中会报错，在IE9+模拟的IE7/8中不会报错。注：IE6-8，包括IE9+的文档模式，无法改变`type`属性值；IE10是根据网上反应，<del>IE11未测</del>。

## 2015-06-05

- `try {} catch(err) {}`中的`err`参数不能省略，否则会报错。

## 2015-06-06

- 去掉/隐藏IE10+中`input`的清除按钮`X`或密码输入框的眼睛，该按钮会出现于IE10+浏览器，设置`documentMode`不会改变此行为，但由于下面的CSS只作用于文档模式为IE10+时，因此还不知道如何清除IE10+中文档模式为IE6-9中的该按钮。

  ```css
  .someinput::-ms-clear,
  .someinput::-ms-reveal {
    display: none;
    width: 0;
    height: 0;
  }
  ```

   暂时不清楚为什么有些人要`display:none`与`width:0;height:0;`都写，参考：http://stackoverflow.com/questions/14007655/remove-ie10s-clear-field-x-button-on-certain-inputs

- 对赋值表达式求值得其右操作对象（right operand），因为左操作对象并不总是等于右操作对象，如：

  ```js
  console.log(document.cookie = 'test');
  console.log(document.cookie);

  console.log(input.value = 0);
  console.log(input.value);

  var obj = {};
  Object.defineProperty(obj, 'value', {
    configurable: true,
    enumerable: true,
    writable: false,
    value: 10
  });
  console.log(obj.value = 0);
  console.log(obj.value);
  ```

## 2015-06-07

- 搜狗高速浏览器5.2.5.15987对于`input:password`右侧会出现一个软键盘按钮，点击出现一个搜狗自己的软键盘，在IE模式下会隐藏掉原先IE10+中的眼睛按钮及`input:text`的`X`清除按钮，不知道怎么通过代码隐藏，像个SB一样自作聪明，对开发者一点儿都不友好。反馈必须通过BBS，真是不爽。

## 2015-06-08

- 如何复制文件夹时排除某些文件或目录？比如排除`.git`, `.DS_Store`等。

  通常可以使用`rsync`，见：http://stackoverflow.com/questions/3672480/cp-command-should-ignore-some-files

  对于想要利用`.gitignore`配置的，且文件夹为一个 git repo，则可以使用`git archive`命令，参考：[http://stackoverflow.com/questions/160608/do-a-git-export-like-svn-export](http://stackoverflow.com/questions/160608/do-a-git-export-like-svn-export)。

## 2015-06-09

- `cp`复制隐藏文件（即`.`字符开头命名的文件）：`cp -r /foo/dir/. bar/dir`

- 点击验证码更换验证码图片，似乎应该等待图片加载好之后再替换`src`，因此需要`img`的`load`事件。

## 2015-06-10

- 设置 Sublime 的光标控制键`Ctrl`+`A`/`E`/`J`/`O`/`I`/`M`，结果导致按`Ctrl`键的频率很高，而且是小拇指在按，如果键盘键程长（普通PC键盘）连续时间一长就感觉酸累。相对来说Vim的光标控制不会这么费力，但是要经常按`ESC`来切换输入模式。

- 发现一些高star数的前端插件/模块写得代码并不好，比如这几天的 jquery.placeholder.js，作为3000多star，几年的项目了，发觉代码里面竟然有逻辑很不严密的地方，几十个人都没人贡献核心的代码。当然，写好一个优秀的模块比较辛苦，也越发佩服那些维护代码几万数十万行的人了。

- 元素的`:focus`样式，通过设置`tabindex`属性来控制元素是否可通过`Tab`获取焦点。但是对`label`元素似乎不起作用，得仔细研究下。

## 2015-06-11

- 输入框获取焦点，也即触发`focus`事件时，将光标放在内容末尾：

  ```js
  input.focus(); // 这行必须首先执行
  var val = input.value;
  input.value = '';
  input.value = val;
  ```

- IE6 不支持`transparent`设置`border-color`

## 2015-06-13

- 函数命名问题：判断一个字符串是否为`""`/`null`/`undefined`，取名`isEmpty()`，而判断一个字符串是否为`""`/`null`/`undefined`以及连续的空白字符串（空白字符是取`/[ \f\n\r\t\v]/`呢？还是现代浏览器中的/\s/）取名`isBlank`。

- [vim]：交换两行，就是上面一行移到下面一行的下面`ddp`或下面一行移到上面一行的上面`ddkP`。

## 2015-06-15

- IE6/FireFox/Safari（测试时使用最新版浏览器）的`input.focus()或input.select()`方法均需要设置一个延迟才会看到效果：

  ```js
  setTimeout(function(){
    input.focus();
  }, 0);
  ```

 -	http://blog.codeblack.nl/post/IE6-focus-problem.aspx
 -	https://forum.jquery.com/topic/input-focus-for-firefox-and-safari-doesn-t-seem-to-work

- 后端如何生成唯一的ID问题？比如生成唯一的用户名，考虑到并发情况，考虑到同一时刻，甚至不同服务器同一时刻问题。

- 允许用户名/邮箱/手机号作为用户ID登录时，如何判断输入的字符串是哪一种？因为历史遗留问题，有可能用户名与另一个用户的邮箱或手机号相同，并且有可能密码都相同。假如之前用户名不能以数字开头，用户名不能是邮箱格式，则不会有这些问题。

- git rebase remote:

  - http://stackoverflow.com/questions/7929369/how-to-rebase-local-branch-with-remote-master
  - https://help.github.com/articles/resolving-merge-conflicts-after-a-git-rebase/

## 2015-06-16

- `seajs.use('foo.js')`与`seajs.use('foo')`在`alias`中配置了`foo`的话两者引用两次`foo.js`文件，执行两次。

- `mousedown`/`mouseup`结合实现的效果，比如鼠标按下时显示明文，鼠标松开显示密文的切换效果，但是有人非要把鼠标移动元素之外再松开，此时不会触发该元素的`mouseup`事件，但是移出会触发`mouseleave`事件，此时也应该有必要触发`mouseup`相同的事件监听器。

## 2015-06-18

- 全屏背景图片，参考LinkedIn的登录页面：

  - 兼容IE6-8：Respond.js
  - 注意图片的高度不能太小

  ```css
  body {
    background: url("bg_1920x960.png") no-repeat top center fixed transparent;
    background-size: cover;
  }

  @media screen and (max-width:1152px) {
    body {
      background-image: url("bg_1152x800.jpg");
    }
  }

  @media screen and (max-width: 1600px;) and (min-width: 1153px) {
    body {
      background-image: url("bg_1600x960.jpg");
    }
  }
  ```

- UTF8的CSS文件加载到GBK页面，结果中文注释导致了文件后面的CSS失效。

## 2015-06-19

- 从`localStorage`中获取搜索历史关键词时，将`keyword`属性必定存在作为前提条件，结果并不一定有，导致关键词数组出现`undefined`值，然后使用一个`encodeHTML`函数处理此值时，没有对字符串以外的值做特殊判断，结果不存在字符串`replace`方法导致报错。这个过程中的任何一步做下判断都可以避免这样：

  - 存储到`localStorage`时，保证存在`keyword`，这个代码已经保证了，还不知道为什么会出现问题
  - 获取历史关键词时，只有`keyword`值不为`null`/`undefined`时或者不能为字符串以外的值时才获取
  - 使用`encodeHTML`方法时做一个判断（这里检测太麻烦）
  - `encodeHTML`方法本身可接受任何类型的值

- 历史数据问题：假如存在两个用户邮件地址相同的话如何处理，另外就是后端插入数据之前检测了是否已存在邮件，没有并发情况，不考虑直接修改数据库的情况下，还有什么原因导致的呢？好难处理。一开始的规则设计很重要。

## 2015-06-22

- IE10+标准模式不再支持条件注释语句

- 最近在写一个`input`事件兼容性模块时，差点误用单例/单例模式，想着模块暴露的`on`和`off`方法共享某些中间变量（主要是IE的事件回调名称）。但单例模式只有一个实例，其属性是共享的，假如模块采用这种模式，则所用调用该模块的位置实际上在共享属性，这可能引起问题。

## 2015-06-24

- 一些浏览器，比如Chrome，会在标签页处于非活动状态时降低`setTimeout()/setInterval()/requestAnimationFrame()`的帧率。

- 对于比较运算符，常量前置的优点是可以避免将`===`/`==`写成`=`，然后通常不会报错，缺点是需要习惯这种写法，一开始觉得这种写法可读性差。另外可以通过工具检测出这种误写的，此时这种写法不一定好多少。

- IE7/8中命名函数相当于声明一个函数：

  ```js
  console.log(window.CustomEvent); // function string ...
  console.log(window.foo); // undefined

  window.CustomEvent = function CustomEvent() {};
  window.foo = function(){};
  ```

## 2015-06-27

- 最近半个月遇到的 JavaScript 问题差不多都错了：

  - 第一个是赋值语句的返回值，而且应该直接查规范或者书的，不要在群里问了，大家都很忙的；
  - 第二个是`Function.prototype instanceof Function`，我之前还做过的
  - 第三个是`var a = 1; a.x = 2;`结果也错了，而且在群里的态度很不好，最后看规范可以理解，但是《JavaScript高级程序设计第三版》上面有讲的，“基本包装类型”

  种种迹象说明三个问题：

  - 看到一个问题的态度，阅读题要仔细，深度分析，然后态度友好地对话
  - 该复习了
  - 看规范可以理解很多

## 2015-07-01

- 一个常见的初级错误：正则表达式不注意`^`和`$`的使用。

## 2015-07-02

- 找回密码/重置密码时，邮箱验证方式发送的链接，该链接页面最好是响应式的或者可以根据设备来返回对应页面，即PC找回时返回PC页面，移动端时返回移动端页面。

## 2015-07-03

- 与后端交互时，注意 loading 动画使用与禁止用户重复发送请求，比如用户点击一个按钮之后，在接受到请求响应前：阻止再次点击，按钮状态改变，显示loading状态等。

## 2015-07-10

- Mac OSX 上 npm 不知到怎么坏了，就是执行`npm`没有反应了，然后最快捷的安装方法就是重新安装 node pkg，npm 集成在里面。通过官网的`install.sh`不能安装，说`rimraf`没有。

- 文件类型探测（file type detection）：根据文件的 magic number 判断。前端JS/后端/Linux的`file`命令都可以做到。

  参考：

  - https://github.com/sindresorhus/file-type
  - https://en.wikipedia.org/wiki/Magic_number_(programming)

- TDI (Tabbed Document Interface) 一个缺点时打开的 Tab 太多时，打开的 Tab 的 Title 会难以查看，另外也难以用鼠标选择。见过多行显示以及 VS/Eclipse 那种隐藏部分Tab的处理方式。

## 2015-07-14

- `$('form').submit(listener)`在 IE6 下不会触发`listener`回调，是因为必须要有`input:submit`元素，可用`$('button#save').click(listener)`替代，还要注意`Enter`的提交动作。

- SVN 获取远程仓库提交记录命令：

  ```bash
  svn log -v -l 5 --xml --username testuser1 --password testpassword <remote-url>
  ```

  上面命令获取额外提交记录信息（如更改文件路径），只获取最近5条记录，以 XML 格式输出，`--username`与`--password`参数如果省略，会提示你输入，具体参数含义及其他参数通过`svn help log`或 Google 查看。

- `float`属性值为非`none`时，会使部分`display`属性的计算值转变为`block`，具体查看：[https://developer.mozilla.org/en-US/docs/Web/CSS/float](https://developer.mozilla.org/en-US/docs/Web/CSS/float)

- 即使为文档根元素（`html`）设置`float: left`，浏览器仍然按照`float: none`来渲染。

## 2015-07-26

- 同一个作用域内，函数声明会覆盖变量声明，而变量声明不会覆盖函数声明：

  ```js
  var foo;
  function foo(){};
  console.log(foo); // function foo()
  ```

## 2015-08-11

- Ubuntu/Sublime Text 3 的 PyV8 模块是 Python3+，Emmet 插件需要，可能会由于墙的问题导致无法安装成功，需要手动下载安装[https://github.com/emmetio/pyv8-binaries](https://github.com/emmetio/pyv8-binaries)，需要注意的是路径应该放到`Installed Packages`下，`Installed Packages > PyV8 > pyv8-linux64-ps`。

- Linux自带的 vim 如果是非GUI版本，通常不支持系统剪贴板，即`vi --version`包含`-clipboard`，可以安装 vim-gnome, vim-athea or vim-gtx，参考：http://vimcasts.org/blog/2013/11/getting-vim-with-clipboard-support/。

## 2015-08-12

- `div`元素默认的`width`为`100%`父容器宽度，`transition`无法使`width`从默认值变化到其他设定的值。

- Chrome Devtools > Sources，每次使用不同的值作为 JS 文件的参数时，加载的是不同的文件，导致必须重新打开，打的断点也没了，所以不能粗暴地使用当前时间戳来刷新静态资源的版本。

## 2015-08-19

- 批量删除：（如果只提供一次点击选择一个的话）没必要选择之后再删除，可以直接点击删除，另外再提供全删除交互。或者，批量选择交互更便捷。

- 页面间共享信息/数据/状态，可通过 Cookie, localStorage，而不要通过 URL

- 配置项的管理：复用，定制等。主要是各种组件都有配置项，有些页面的配置项是相似的。

## 2015-08-27

- `word-wrap`与`word-break`区别：测试数据<td>openapi����������ѯ�б�����������ӿ�Ƶ�ʿ���</td>

## 2015-08-28

- `click`事件是可取消的，即鼠标按下时移开元素范围，就不会触发，而`mousedown`事件按下机触发。

## 2015-08-29

- `link`/`ln`命令的实际文件路径（source_file）用绝对路径

- gem 安装 sass 报错，需要梯子：

  ```bash
  chaoalex:rubygems-2.4.8$ sudo gem install sass
  ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
      Errno::ECONNRESET: Connection reset by peer - SSL_connect (https://api.rubygems.org/quick/Marshal.4.8/sass-3.4.18.gemspec.rz)
  ```

## 2015-08-31

- 元素`div.overlay`应用下面CSS设置，则它会向四个方向扩展，直到覆盖定位父元素。

  ```css
  .overlay {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }
  ```

## 2015-09-01

- underscore 的`_.template`函数第二个参数从1.7.0开始不再接受一个数据对象，返回值永远是一个函数，原因看：//cdn.bootcss.com/underscore.js/1.8.2/underscore.js。其他注意 changelog，尤其是underscore不支持semver规范。

## 2015-09-13

- `input:radio`与`select`区别：

  - `input:radio`：一次操作完成选择，但占用空间；
  - `select`：两次操作完成，节省空间，适合选项很多时。

## 2015-10-08

- `z-index`的全局规划，常见需要设定`z-index`属性的组件有：

  - 固定元素：导航栏、侧边栏或其他元素
  - 弹出层：菜单、弹框、提示框等
  - 遮罩
  - 系统组件：`select`, `input:date`等

  可以参考 Material Design。

- Modal 组件在出现时应该获取焦点，比如：点击一个弹出的菜单-->出现Modal，通常菜单组件是在失去焦点时自动关闭，但如果Modal不显示调用`.focus()`方法获取焦点，在菜单组件不会消失。

## 2015-10-09

- 几乎所有使用图标/简写词的位置都加上`title`属性或 tip 组件可以照顾小白用户，或者图标表达不清晰时。

- [svg] `g`与`svg`嵌套时，`svg`的`x`属性与`g`元素的`transform`属性作用顺序

  http://tutorials.jenkov.com/svg/g-element.html#g-drawback

## 2015-10-12

- 数组遍历相关方法[`forEach`/`filter`/`map`等]调用过程中使用`splice`方法删除了某些元素，则会跳过某些元素，比如：

  ```js
  var arr = [1, 2, 3];
  arr.forEach(function(value, index) {
    console.log(value);
    if (value === 1) arr.splice(index, 1);
  });
  // Print:
  // 1
  // 3
  ```

  即跳过了第二个元素. 另外，在使用 underscore 的`each`方法遍历时，由于其缓存了数组的长度，所以会越界访问

- jQuery event namespace：
  ```js
  $(document).on('click.myNamespace', ...);
  $(document).on('hover.myNamespace', ...);

  // 解邦所有 myNamespace 命名空间下的事件
  // 注意前面的点不能少
  $(document).off('.myNamespace');
  ```

## 2015-10-18

- git clone github.com 上面的 repository 报下面错误，打开VPN或配置git 的代理：

  ```
  chaoalex:node$ git clone https://github.com/gruntjs/gruntjs.com.git
  Cloning into 'gruntjs.com'...
  fatal: unable to access 'https://github.com/gruntjs/gruntjs.com.git/': Could not resolve host: github.com
  ```

  或

  ```
  chaoalex:node$ git clone git@github.com:gruntjs/gruntjs.com.git
  Cloning into 'gruntjs.com'...
  ssh: Could not resolve hostname github.com: nodename nor servname provided, or not known
  fatal: Could not read from remote repository.
  ```

## 2015-10-20

- 去掉`outline`显然是对可访问性很不好，那自定义一种样式呢？参考：

  - http://tjvantoll.com/2013/01/28/stop-messing-with-the-browsers-default-focus-outline/
  - https://drafts.csswg.org/mediaqueries-4/#pointer

  默认的样式确实对大部分正常的人来说很难看，最好能区分设置两种样式。

## 2015-11-03

- 表单(form)的提交应该使用`submit`事件，而`<input>`里按`Enter`/`Return`键和点击`input:submit`/`button:submit`的`click`事件都会触发表单提交。另外 Ajax 提交表单还要阻止表单的默认提交`event.preventDefault()`。

## 2015-11-06

- 所有的删除操作必须要确认一次吗？另外的处理方式是，增加回收站，或者学习 Gmail 的，5s内可恢复。

- jQuery没有`hover`事件，有`hover()`方法。

## 2015-11-11

- 常用的基础组件有哪些呢？PC端前台项目与后台项目不同，不同行业项目也不同，PC端与移动端也是不同的。目前经验是后台管理项目前端组件如下面列表，另外有些是纯粹的UI增强，非必须的，如 checkbox，当然这些在实际项目还要根据需要写更多其他的。

  - module system
  - util：类似于 lodash/underscore，但是方法更多
  - browser：浏览器识别
  - storage：cookie/localStroage/sessionStorage
  - template
  - history
  - router
  - animation
  - Dropdown
  - Tab
  - Slide
  - Modal
  - Position
  - Popup
  - Popover
  - Tooltip
  - Popconfirm
  - Alert
  - Loading
  - Message
  - Collapse
  - Affix
  - Validate
  - Autocomplete
  - Datetimepicker
  - Checkbox/Radio/Select
  - Switch
  - Progress
  - Tag
  - Pagination
  - Batch check
  - File uploader
  - Image Crop
  - Drag and drop
  - Input clear button
  - clipboard
  - DataTable
  - Chart
  - Editor
  - ImageBox
  - Tree

- Git 回滚操作:

  比如 Master 的记录为：A->B->C->D->E，还有其他分支，采用 Gitflow 方式开发

  - 从 E 回到 B，如何操作
  - 取消 B，C 两次 Commit 的改动

## 2015-11-12

- `getter`/`setter` style：

  **合并为一**

  ```
  foo(); // getter
  foo(10); // setter
  ```

  ** 两个独立函数**

  ```
  getFoo(); // getter
  setFoo(10); // setter
  ```

  合并为一个写起来简单，两个独立的读起来直观，但是明白不传递值时为`getter`，传递值时为`setter`读起来并非不直观，或许这只是一种风格，一种与语言相关的风格。

  参考：http://stackoverflow.com/questions/1610029/getters-and-setters-style

## 2015-11-13

- 文本或 URL 太长截断问题：

  ```
  .dont-break-out {
    overflow-wrap: break-word;
    word-wrap: break-word;
    
    -ms-word-break: break-all;
    word-break: break-all;
    word-break: break-word;

    -webkit-hyphens: auto;
    -moz-hyphens: auto;
    -ms-hyphens;
    hyphens: auto;
  }
  .ellipsis {
    overflow: hidden;
    white-space: nowrap;
    text-overflow: ellipsis;
  }
  ```

  参考：[https://css-tricks.com/snippets/css/prevent-long-urls-from-breaking-out-of-container/](https://css-tricks.com/snippets/css/prevent-long-urls-from-breaking-out-of-container/)

## 2015-11-25

- `let` vs `var`：在 ES2015 的新代码中使用`let`替换`var`，旧代码不能盲目替换，当然应该很少人去这样做，一篇相关文章：[For and against `let`](https://davidwalsh.name/for-and-against-let)，说实话，文章错误不能替换的原因个人不太认同。

- `JSON.parse()`不能解析空白字符串，如`""`/`"\n"`，而使用`fs.readFile()`读取空文件时，字符串形式数据为`""`（该空文件通过`touch`命令创建）。

## 2015-11-27

- 直接使用`str.trim()`方法的缺点就是，假如`str`不是字符串时会报错。

- 通过 POST/PUT 传递到后端的数据，全部用字符串类型，后端也应该这样假设。

## 2015-12-01

- 服务端搜索还是浏览器端搜索？被搜索的数据是否会改变？如果会改变，数据一致性要求高吗？如果高则必须采用服务端搜索。

- 不会保持模块细颗粒度，抽象能力太弱，导致业务代码倾向冗余或者难于复用。比如做了一个功能A，后来又有需要做一个功能B，B与A有些相似性，但由于A功能的代码难于复用，导致功能B又要做些相同的事情，当然时间成本不一定增加很多，因为可以复制代码。后来，想要精简代码，就需要重构了，这个时候成本就增加了。

## 2015-12-02

- JSON VS JavaScript：JSON 不是 JavaScript 的子集，有效的 JSON 请参考 JSON 规范，比如`{'a': 2}`不是有效的 JSON，应该使用双引号，比如需要转义的特殊字符只能是：`"`, `\\`(反斜杠，此处两个为了 Markdown 显示）, `/`, `b`, `f`, `n`, `r`, `t`, `uxxxx`，即反斜杠`\\`后面不能跟其他字符。

## 2015-12-03

- mongoosejs model 查询方法的 API 设计：

  当一个`calbback`函数：
  - 传入时，会立即执行查询操作，结果会传给回调函数。
  - 没有被传入时，返回一个`Query`的实例，该查询可以执行，`Query`可以混合 promise 接口。

  另外，没有传入回调函数时，该函数也可以返回查询操作结果，也是一种接口方式。

## 2015-12-11

- `Function.prototype.bind`返回值是一个**bound function**，这个函数再次调用`bind`，其`this`不会被覆盖，但是每次传递的参数会改变。

- 函数属于引用类型：

  ```js
  var obj1 = {
    foo: function() {
      console.log(this.value);
    }
  };
  var obj2 = obj1;
  var obj3 = {};
  obj3.foo = obj1.foo;

  obj1.foo = obj1.foo.bind({ value: 1 });
  obj2.foo = obj2.foo.bind({ value: 2 });
  obj3.foo = obj3.foo.bind({ value: 3 });

  console.log(obj1.foo()); // 1
  console.log(obj2.foo()); // 1
  console.log(obj3.foo()); // 3
  ```

## 2015-12-14

- Webpack 有时在文件改动后不重新编译，是 watch 的问题，解决方法：https://webpack.github.io/docs/troubleshooting.html。

## 2015-12-15

- URL Query <=> Form => Table：

  - 根据 URL 查询条件决定表单元素的值，决定表格显示的数据
  - 根据表单元素的值决定 URL 查询条件，决定表格显示的数据

- 不要使用语言保留字作为属性，作为数据的 key（针对后端返回数据），IE8里面不支持 JS 保留字作为对象属性时，用`.`访问。

## 2015-12-25

- 一个比较复杂的项目可以有一个常用变量表，有些变量必须用很长的单词组合才能表达出其代表的含义，而取首字母或前几个字母的缩写方式不太直观，所以有个这种表还是有用的。

- 使用`localStorage`或`sessionStorage`时改变了某个 key 的数据结构，则用户刷新到更新版本时可能产生报错，可以弄个只清理一次本地存储的方法。

## 2016-01-05

- 已经多次测试 Ajax 请求时忘记删除测试代码：
  - 比如直接给请求返回结果赋值一些测试数据
  - 比如提交数据时，提交测试数据

  这种方法有问题


- Reload .vimrc file without restarting vim?

  `:so %` or `:so $MYVIMRC`

## 2016-01-11

- Babel 目前不支持`Object.assign`，还不支持其他一些 ES6，可以查看 Babel 主页看支持哪些特性，可参考 kangax 的兼容性表格。

  对于如何支持`Object.assign`的问题，可使用 jQuery/object-assign/lodash/core-js 等解决方案。

## 2016-01-22

- 调试用赋值工具，比如 Ajax 返回的数据，有时服务端没有数据，此时希望测试有数据时的样子，为了简便直接使用构造的数据（赋值给了 Ajax 成功回调数据），如果忘记删除，就可能上线了。如下工具解决：

  ```js
  debug.assign(res.data, [{ name: 'foo' }, { name: 'bar' }]);
  ```

  debug 模块的所有功能只在线下环境有效，至于如何区分线上线下，可通过域名，或查询参数。

## 2016-02-02

- Modal 内容为表单，打开时自动获取焦点（`autofocus`），但是输入框有内容时，光标在内容最开头，这可能不是想要的，期望效果是内容末尾或选中内容。

- `background-position`：CSS3 扩展了其值，支持 0~4 个值，详见 [https://drafts.csswg.org/css-backgrounds-3/#the-background-position](https://drafts.csswg.org/css-backgrounds-3/#the-background-position)。

## 2016-02-14

- Bootstrap v4-dev 的`_buttons.scss`为什么使用`.btn:active:focus`，是否多余？

## 2016-02-15

- Mac OSX 平台下，不设置环境变量`EDITOR`时，默认编辑器为`vi`，其使用自己的`~/.vimrc`配置导致`exit code`为 1，这样`git commit`或`crontab -e`命令总是无法正常使用，可以在`.bash_profile`文件中设置`export EDITOR=/usr/bin/vim`解决。

## 2016-02-17

- 在实际项目中自己写一个组件，由于经验（无论应用还是设计，尤其是没有可参考对象时）不足，结果各方面设计的都很不好，比如命名不好、扩展性不好等等，如果升级的话难于与前面的版本兼容，兼容会使代码很糟糕，以后更难维护，这些情况该怎么管理呢。

- OSX 文件系统不区分文件大小写，所以永远避免仅仅大小写不同的文件命名。

## 2016-02-26

- 越是基础，应用于很多个业务模块的模块，越应该提早设计好。


## 2016-03-22

- Git 在文件结构改变时分支操作注意顺序，比如：

  **branch-a**分支下有`/client/images/logo.png`文件，而**branch-b**分支已经改成了`/images/logo.png`路径，然后分支**develop**先合并**branch-b**，然后应该先在分支**branch-a**下执行`git rebase develop`，再在分支**develop**下合并**branch-a**。

## 2016-03-24

- 通过 shelljs 的`exec`方法或 Node 的`child_process.exec`方法执行一个 Bash 脚本，Bash 脚本中执行 npm scripts，结果报错如下：

  ```
  npm ERR! Linux 2.6.32-431.el6.x86_64
  npm ERR! argv "/usr/bin/node" "/usr/bin/npm" "run" "build"
  npm ERR! node v5.7.1
  npm ERR! npm  v3.6.0
  npm ERR! file sh
  npm ERR! path sh
  npm ERR! code ELIFECYCLE
  npm ERR! errno ENOENT
  npm ERR! syscall spawn sh
  npm ERR! odin-ui@1.0.0 build: `gulp build`
  npm ERR! spawn sh ENOENT
  npm ERR! 
  npm ERR! Failed at the odin-ui@1.0.0 build script 'gulp build'.
  npm ERR! Make sure you have the latest version of node.js and npm installed.
  npm ERR! If you do, this is most likely a problem with the odin-ui package,
  npm ERR! not with npm itself.
  npm ERR! Tell the author that this fails on your system:
  npm ERR!     gulp build
  npm ERR! You can get information on how to open an issue for this project with:
  npm ERR!     npm bugs odin-ui
  npm ERR! Or if that isn't available, you can get their info via:
  npm ERR!     npm owner ls odin-ui
  npm ERR! There is likely additional logging output above.
  npm ERR! Linux 2.6.32-431.el6.x86_64
  npm ERR! argv "/usr/bin/node" "/usr/bin/npm" "run" "build"
  npm ERR! node v5.7.1
  npm ERR! npm  v3.6.0
  npm ERR! code ELIFECYCLE

  npm ERR! odin-ui@1.0.0 build: `gulp build`
  npm ERR! Exit status -2
  npm ERR! 
  npm ERR! Failed at the odin-ui@1.0.0 build script 'gulp build'.
  npm ERR! Make sure you have the latest version of node.js and npm installed.
  npm ERR! If you do, this is most likely a problem with the odin-ui package,
  npm ERR! not with npm itself.
  npm ERR! Tell the author that this fails on your system:
  npm ERR!     gulp build
  npm ERR! You can get information on how to open an issue for this project with:
  npm ERR!     npm bugs odin-ui
  npm ERR! Or if that isn't available, you can get their info via:
  npm ERR!     npm owner ls odin-ui
  npm ERR! There is likely additional logging output above.

  npm ERR! Please include the following file with any support request:
  npm ERR!     /root/odin-ui/npm-debug.log
  ```

  最后发现使用`exec`方法时需要传入的`env.PATH`中加上`[package_root]/node_modules/.bin`路径。

## 2016-03-31

- Git commit 是一个 Snapshot 的 hash 字符串，branch 是一个可移动的指针，指向 commit，当前分支 HEAD 所引用分支。

## 2016-04-07

- 为什么 Grunt 与 Gulp 不再流行？Webpack 相比他们有什么优势呢？感觉 Webpack 只是功能变得更强大了，并没有解决 Grunt/Gulp 存在的问题。插件生态问题，学习成本。

## 2016-04-12

- [Git] 假如有两个分支`dev`和`feature`，其中`dev`分支合并了`feature`分支后有合并了其他几个 Commit，`feature`之前开发是`git rebase dev`的。后来需要从`dev`去除`feature`分支引入的改动，采用了`git revert`命令，然后接着开发`feature`时，无论是执行`git rebase dev`还是`git merge dev`分支，都会因为之前在`dev`分支执行过`git revert`导致删除之前开发的代码。此时可以再 Revert Revert。

## 2016-04-14

- 一个`textarea`添加对`Enter`/`Return`快捷键事件时小心，仍然会插入一个换行符，如果调用`event.preventDefault()`则不能正常换行，此时可使用快捷键`Shift + Enter/Return`并阻止默认事件动作。

## 2016-04-28

- 一个请求的完整过程，从发出到接受到响应，有助于调试，包括经过多层代理，包括代理对不同类型链接的影响，还有浏览器缓存、操作系统 DNS 缓存、hosts 文件、杀毒软件等各种影响。调试的时候要跟着请求的过程来一步一步调试。

- git 流程优化：下面的操作很频繁，可以组合为一个命令

  ```shell
  # Current branch idc-network
  # After change

  git add -A && git commit -m "Update 1"

  git checkout sandbox

  # Current branch sandbox

  git merge idc-network
  git push origin master
  ```

## 2016-04-29

- 接口 API 设计时考虑到返回多条数据的接口，尽量减少前端与后端的请求数。

- 组件的可扩展不是随便暴露个方法就完事了，要考虑如何快捷便利地扩展。

## 2016-05-17

- 常见的 jQuery 组件的回调或参数值为函数时，函数的`this`最好统一为组件的实例对象。

## 2016-05-18

- 假如要在同后端交换（获取/提交)的 JSON 数据上添加只在前端使用的属性，即前端私有属性，比如命名下划线开头`_`，但是后端也可能返回下划线开头的属性，那应该防止命名冲突。

## 2016-05-23

- 数据为什么要有（创建/更新）日期字段？日期经常作为排序方式，且很实用。

## 2016-05-24

- AJAX API Path 格式：最好在一个子域名或目录下面，比如`api.domain.com`或`www.domain.com/api`，因为获取数据的 Ajax 与普通的页面请求某种角度看有区别，这个区别应该包含进来。

  - 与正常的页面、静态资源等请求区分，如子域名或子目录下面。
  - 版本管理，考虑到 API 将来会变更，通过 Path 或 Query string 区分，如`/v1/article/1`或`/article/1?version=1`。
  - 是否需要授权访问，通过 URL 反应出来，如`/auth/v1/article/1`或`/v1/article/1?auth=true`。（个人倾向前一种）

- React-router 解析 Querystring：

  ```js
  let { ns, isLeaf } = this.props.location.query;
  ```

## 2016-05-25

- NodeJS 中`__dirname`与`process.cwd()`区别：

  - `__dirname`：文件所在目录
  - `process.cwd()`：代码执行时的当前工作目录

## 2016-05-27

- 环境：

  - OS: Linux kvm32140.jx.somedomain.com 2.6.32-431.el6.x86_64 #1 SMP Fri Nov 22 03:15:09 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
  - Node: 5.6.0
  - npm: 3.6.0
  - gcc/g++: 4.4.x

  在安装包`selectize`的依赖的依赖`microtime`（C++写的）时，总是编译出错，后来升级 gcc/g++ 到 4.8.2 之后编译通过。

## 2016-05-31

- 用户输入字符串时，要考虑是否自动清除空格。

## 2016-06-01

- 应用会有很多 Ajax 请求，这些请求或请求的 URL 应该统一管理（放入一个模块/文件）还是分散在各业务模块。

## 2016-06-03

- 使用 Ant Design 时，一个常见的情形是弹层里面有个表单（Form in Modal），假如有个表单元素是 Select，那么如何在每次显示弹层时，Select 都恢复初始状态。

- 单页面中：弹层 VS 新页面，代码量，使用难度，比如 ant-design 的 Modal 组件

- Ant Design 的 Message 组件不可以相对于某个元素定位，或者 Tooltip 组件得一个元素对应一个，不能多个元素对应一个，但是内容不同。这是否可以看成是 JSX 语言的劣势或者说 Ant Design 实现方面的劣势。

- 即时在移动式优先的今天，也应该考虑大屏显示器，比如常见的 PC 显示器，即通常内容应该有个最大宽度，这样浏览和使用起来体验更好，否则需要经常扭头。

- [React router]: 子路由与子组件以及 URL 之间的关系：

  比如有两个页面：一个列表页面，一个详情页面。希望设置的路由为：

  ```
  /order - 列表页面
  /order/detail/:id 或 /order/:id - 详情页面
  ```

  两个 React 组件为：Order.jsx 和 OrderDetail.jsx。其实希望文件结构为：

  ```
  - order/
    |- Detail.jsx
  - Order.jsx
  ```

  但是路由必须这样写：

  ```js
  let routeConfig = [
    {
      path: '/order',
      component: Order
    },
    {
      path: '/order/:id',
      component: OrderDetail
    }
  ];
  ```

  如果写成下面这样：

  ```js
  let routeConfig = [
    {
      path: '/order',
      component: Order,
      childRoutes: [
        {
          path: '/:id',
          component: OrderDetail
        }
      ]
    }
  ];
  ```

  则 React 会先渲染 Order 组件，除非 Order 组件里面显示了`this.props.children`，否则 OrderDetail 不会渲染。

## 2016-06-08

- ESLint 命令行指定一个配置文件，只是会将该配置文件与已有的配置合并，比如当前目录下有 .eslintrc 的化，.eslintrc 的配置仍会起作用。

## 2016-06-14

- `!important`只有没有其他选择时才使用。

- 注意 CSS 选择器影响的范围，如果在某个特殊的场景下某个组件样式有问题，则不应该更改该组件的样式，避免影响全局。

## 2016-06-15

- [API 设计]：jQuery 的很多方法，如`attr()`/`addClass()`/`css()`等对整个 jQuery 对象生效，jQuery 对象是个集合/数组，几十集合内一个元素也没有也不会报错。

- [API 设计]：添加一个和添加多个方法，可以定义两个：`addItem(value)`和`addItems(values)`，也可以定义一个支持两种参数（类似重载）：`addItem(value`，那种更好呢？

## 2016-06-16

- 网站帮助系统设计：

  - 历史问题记录
  - 搜索
  - UGC

- 网站公告系统

- 自己开发组件库 VS 使用第三方

  自己开发组件：可以快速实现定制需求，但是前期工作较慢。

  第三方：前期快速开发，但是定制需求很慢。

  例外，开发者有自己的一套组件库或者对第三方的组件有很深的研究。

- 建立一套测试系统非常非常重要，可提升开发效率，可快速检测出错误，尽量与线上环境一样，并且数据要模拟的真实。

- URL 长度限制：避免 GET 请求的查询参数过长问题

  - 浏览器地址栏限制
  - Ajax 请求限制
  - 代理服务器限制
  - Nginx/Apache 服务器限制
  - 后端框架限制
  - 等等

## 2016-06-29

- npm3.x 总是出现 unmet dependency 警告：http://stackoverflow.com/questions/20764881/why-does-npm-install-say-i-have-unmet-dependencies。

- Ajax 接口无权限时不能返回 302，因为浏览器会自动跳转，参考：http://stackoverflow.com/questions/8238727/how-to-prevent-ajax-requests-to-follow-redirects-using-jquery#answer-8241461

- 尽量避免使用`$.ajaxSetup()`，可使用`.ajaxStart()`/`.ajaxComplete()`之类绑定回调。

## 2016-07-04

- Webpack 使用 loader 时，如何区分字体的 svg 与图片的 svg 文件呢

## 2016-07-05

- d3 的 UMD 定义就很好，如果是 AMD，就在额外执行`this.d3 = d3;`，这样在浏览器中就有了全局的`d3`变量，其他依赖 d3 的组件可以正常执行，也不用什么 ProvidePlugin，expose-loader 之类。

## 2016-07-13

- 目前公司的一个项目创建一个新业务模块的过程：

  - 添加导航入口链接
  - 创建业务模块**js**文件
  - 创建业务模块**模板（html）**文件
  - 创建业务模块**样式（CSS/SASS/LESS）**文件
  - 然后经常需要在这些文件之间切换

## 2016-07-14

- 所有显示不定长内容的地方都应该考虑内容太长时如何处理显示问题。

## 2016-07-27

- 起始为`0.8`，终止值为`5.2`，步长为`0.4`，生成一个数列。
    
## 2016-08-02

- 一个列表数据展示，考虑排序问题，可能字母顺序，可能部分高频在顶部，剩下按照字母排序，看情况选择排序策略。

- 关于表单的需求，很多人总是提得不详细导致作为前端的我需要反复沟通确认：

  - 字段的数据类型：字符串，数字还是什么；
  - 字段对应的`label`/`name`属性；
  - 使用的交互元素/表单元素/其他组件；
  - 提示文字：placeholder、tooltip等方式；
  - 是否是必填的，必填是否与非必填区分；
  - 表单验证规则及反馈信息，反馈信息展示方式；

## 2016-08-23

- 单页面应用切换页面白页问题分析：

  - 新的页面（模块）时会异步加载一个模块（js），通过 Webpack 打包的
  - 结果该模块不存在了（因为此时前端上线了新代码，重新构建部署，老文件都会删掉）
  - 然后服务器返回 302 重定向到首页
  - 然后首页的HTML内容当成JS执行，报错`Uncaught SyntaxError: Unexpected token <`

  结论：一是不应该返回 302，二是应该保留一段时间旧文件。

## 2016-08-30

- `order`属性在`inline-flex`容器中没起作用，测试于 Chrome Dev 55
- `flex-grow`：该属性在`inline-flex`容器中不起作用，这合理，因为容器宽度根据内容宽度计算的。

## 2016-09-12

- 命名与抽象层级：比如一个 DBA 使用的工单审核系统，一开始只有简单的发送工单功能，于是文件名就叫`db/order.js`，后来又添加了审核于是合理的命名是`db/send.js`和`db/audit.js`（假如按文件分模块）。为啥一开始不直接命名`db/send.js`呢？可能是没有经验，也有没有多加思考，这样随着系统复杂，需要更高的抽象，然后命名也需要变化，不仅仅文件的名字，还包括函数名、变量名等。

## 2016-09-30

- 类似 ES5 ES6 polyfills，以及 ajax、extend 等方法应该在一个地方集中管理，并经过严格测试，为了以后随时可以使用。重写很容易犯错误，使用别人现成的总是提供多余的功能，导致体积比较大。

## 2016-11-07

- Server Sent Events VS 轮询：假如 Server Sent Events 是用来推送周期性的数据，则不如轮询简单。

