##2014-09-28

发现 grunt-seajs-fresh 要兼容 seajs 配置的各种情况，真的很复杂，有必要考虑所有情况吗？
    (兼容paths与vars，兼容alias与map不是很难，可能极端情况较少考虑）

##2014-09-30

nodeunit很不好用，废弃之，作者的一篇教程已经过时，且至少有两个错误，
    错误很难解决，用得人少，作者也不理

##3014-10-08

node update：`npm install -g n` then `n 0.10.32`，另外也可以由高版本切换到低版本

大概从1.4.13版本开始，npm 默认安装包时不会显示请求，可以：

```bash
npm install --loglevel http
```
或修改.npmrc：

```bash
loglevel = http
```
