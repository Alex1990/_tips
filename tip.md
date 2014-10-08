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

- remove BOM: `text.replace(^\uFEFF/, '');

- 将windows[\r\n]或osx[\r]系统下换行符替换成linux[\n]下换行符:
```js
if (text.indexOf('\r') > -1) {
    text.replace(/\r/g, '');
}
```
