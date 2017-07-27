# 如何快速找出配置文件

[点击看原文](https://huoding.com/2017/07/26/626)

最近使用 pip 的时候感觉速度太慢了，感觉有必要改成豆瓣的镜像，可我记不清 pip 的配置文件路径了，当然可以用搜索引擎查询一下，不过还有更快的方法：strace！

```
shell> strace -eopen pip 2>&1 | grep pip.conf
open("/etc/xdg/pip/pip.conf", O_RDONLY) = ...
open("/etc/pip.conf", O_RDONLY)         = ...
open("/root/.pip/pip.conf", O_RDONLY)   = ...
open("/root/.config/pip/pip.conf", O_RDONLY) = ...
```

如此一来，我们很容易就能看出配置文件的路径，甚至还能看出多个配置文件的加载顺序。此外，通过此技巧还能看到一些意想不到的信息，比如 nginx 的例子：


```
shell> strace -eopen /usr/local/nginx/sbin/nginx 2>&1 | grep conf
open("/usr/local/nginx/conf/nginx.conf", O_RDONLY) = ...
open("/usr/local/nginx/conf/mime.types", O_RDONLY) = ...
open("/etc/resolv.conf", O_RDONLY)      = ...
open("/etc/nsswitch.conf", O_RDONLY)    = ...

```

除了 nginx.conf 等常见的配置文件，我们还可以发现其加载了 nsswitch.conf 等不常见的配置文件，通过研究这些细节，可以让我们更透彻的理解问题。
