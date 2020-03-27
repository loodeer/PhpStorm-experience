# xdebug 调试





![img](https://tva1.sinaimg.cn/large/00831rSTly1gd8b74yhjig30g6066q2y.gif)



原理大致就是上图，具体可自行查阅，这里不多说。



这里实际操作一下如何配置 xdebug，并且介绍几个调试技巧。



### 1. 准备工作

新建一个项目 xdebug_demo，新建 public 目录，建一个 index.php，写一个 for 循环。

![image-20200327122405018](https://tva1.sinaimg.cn/large/00831rSTly1gd8ddd3ilpj30kh07qn0j.jpg)

使用 docker，让项目跑起来。

![image-20200327114448542](https://tva1.sinaimg.cn/large/00831rSTly1gd8c8ib1nrj318q02a768.jpg)

```bash
docker run -d --name myweb -p 8086:80 -p 3309:3306 -v $(pwd):/var/www/html/app laraedit/laraedit
```



![image-20200327122502724](https://tva1.sinaimg.cn/large/00831rSTly1gd8decyrjyj309609rmxe.jpg)

### 2. 服务端配置

```bash
docker exec -it myweb /bin/bash
```

进到 docker 里，确认已经安装了 `xdebug` 扩展。

![image-20200327122807625](https://tva1.sinaimg.cn/large/00831rSTly1gd8dhkpewij30ke02maan.jpg)

开始配置。

```bash
vim /etc/php/7.0/fpm/conf.d/20-xdebug.ini
```

增加以下配置:

```ini
xdebug.remote_enable = on
xdebug.remote_host = 192.168.22.125
xdebug.remote_autostart = 1
xdebug.remote_port = 9001
xdebug.remote_log = /home/tmp/xdebug.log
```

其中 `192.168.22.125` 是我主机的 ip。

改完重启 fpm。

```bash
/etc/init.d/php7.0-fpm restart
```



### 3. IDE 配置

服务端配置完了，再来配置 IDE，只需要修改监听的端口号。

![image-20200327123806471](https://tva1.sinaimg.cn/large/00831rSTly1gd8drylw2tj31hc0tdqky.jpg)

修改完之后，打开绿色小电话，浏览器再访问一次 http://localhost:8086/ 即可 debug 了。

![image-20200327132131997](https://tva1.sinaimg.cn/large/00831rSTly1gd8f158uotj31hc0tdaur.jpg)

![image-20200327132204516](https://tva1.sinaimg.cn/large/00831rSTly1gd8f1prrjij31hc0td4qp.jpg)



### 4. Cli 模式调试

[Debugging a PHP CLI script 官方文档](https://www.jetbrains.com/help/phpstorm/debugging-a-php-cli-script.html)

在 docker 里执行以下命令设置 debug 相关环境变量。

```bash
export XDEBUG_CONFIG="remote_enable=1 remote_mode=req remote_port=9001 remote_host=192.168.22.125 remote_connect_back=0"
```

在 `/var/www/html/app` 目录下执行：

```bash
php public/index.php
```

![image-20200327144826636](https://tva1.sinaimg.cn/large/00831rSTly1gd8hjkyp93j31hc0tdx4d.jpg)

断点成功。



### 技巧1：修改变量的值

在调试过程中是可以修改变量的值的，这一点在你想要进到一些特定的 if 条件时很有用。

![image-20200327133455539](https://tva1.sinaimg.cn/large/00831rSTly1gd8ff33otkj312o0taqqh.jpg)

### 技巧2：设置带条件的断点

很多时候，在循环里我们只想调试具体的某一次，设置 Condition 即可。

![image-20200327134936531](https://tva1.sinaimg.cn/large/00831rSTly1gd8fucvhzoj31hc0tdh7y.jpg)



### 技巧3：快速执行到指定代码

Run to Cursor 可以快速执行到光标所在的行。

![image-20200327133702403](https://tva1.sinaimg.cn/large/00831rSTly1gd8fha9jsxj30uw0u04qp.jpg)

### 技巧4：执行表达式

可以执行任意代码，也可以把 if 的判断扔进去执行一下，非常方便。

![image-20200327135421843](https://tva1.sinaimg.cn/large/00831rSTly1gd8fzb3du5j31hc0tdnh8.jpg)

