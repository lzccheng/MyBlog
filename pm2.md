### pm2
pm2是一个node的进程管理器，可实现多进程运行、后台运行、进程监控等功能。

官网：http://pm2.keymetrics.io/

命令可自行在官网查询，下面是我遇到的问题及解决方法：

##### pm2 运行npm命令：
```js
pm2 start npm -- run start
```
如果在pm2中有`Created by npm, please don't edit manually`的报错，可借助`node-cmd`包
```js
//start.js
require('node-cmd').run('npm start')
```
然后运行这个js即可：`pm2 start start.js`
