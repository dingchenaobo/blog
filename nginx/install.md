## install
```bash
brew install nginx
```

## 路径
* www：/usr/local/var/www。
* 实际安装目录：/usr/local/Cellar/nginx，进去是nginx的版本，进去就是该版本nginx的文件，html文件的其实是个快捷方式，指向的就是上面的www文件夹。
* 配置文件目录：/usr/local/etc/nginx。其中比较重要的就是nginx.conf。

## nginx.conf

## 常用命令
启动
```bash
nginx
```

查询进程号
```bash
ps -ef|grep nginx
```

输出结果
```bash
501 69087     1   0  2:12下午 ??         0:00.00 nginx: master process nginx
501 69088 69087   0  2:12下午 ??         0:00.01 nginx: worker process
501 69956 69375   0  2:30下午 ttys000    0:00.00 grep nginx
```
其中```69087```为nginx主进程号，结束nginx进程：

```bash
# 正常停止
kill -QUIT 主进程号
# 快速停止
kill -TERM 主进程号
```
重启
```bash
nginx -s reload
```
