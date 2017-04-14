# 记一次SSH相关问题解决过程

## 背景

需要利用git clone从某个项目拉取代码，因为项目可能是私有的，所以为每个项目生成了各自的ssh key pair。执行的时候会将各自的ssh key pair保存在用户的`~/.ssh/`目录下 。

新建的秘钥文件名为: builder builder.pub, 用户名beacon@beacon
## 问题描述

调用`git clone`时仍会询问密码，说明key pair没起作用。

## 问题解决

一开始发现可能是格式有问题，解决这个问题之后，仍然不能正常工作。

总是报告"Agent admitted failure to sign using the key", 查了一下，说是要用ssh-add把新创建的密钥加进去。

执行`ssh-add -l`， 发现beacon@beacon赫然在列，以为已经加进去了。

```
2048 4c:fb:7b:3c:1f:24:5e:ba:54:aa:95:8a:61:ef:ae:24 beacon@beacon (RSA)
2048 15:7a:11:77:f3:47:e5:cc:b6:30:2a:81:ee:9e:07:51 bluven@bluven (RSA)
```

遂怀疑是生成的密钥对还是有问题，期间又尝试了
```
    ssh-keygen -y -f builder
    ssh-keygen -lf builder
```
来校验密钥对，都没问题。

后来想到看ssh相关日志，一个日志是`/var/log/auth.log`，另一个是ssh的日志，可以这样看

```
ssh -vT git@git-server
```

还是没有发现问题。

后来仔细看了下ssh-add的帮助，发现`ssh-add -l`只是把指纹列了出来，但是列出来的指纹未必是加入的。
这个理解其实是错误的，但当时这个理解促使我做了如下尝试：

```
ssh-add ~/.ssh/builder
```

成功了。

问题貌似解决了，但还是有些谜团。比如，ssh-agent，ssh-add是跟ssh-agent配合使用的，但在尝试各种解决方案的过程中，ssh-agent的进程全被我干掉了，但我的账户依然可以使用ssh-add命令。我尝试过用其他账户登录，在没有ssh-agent的情况下，ssh-add是不能工作的。

后来偶尔查看了下SSH_AUTH_SOCK变量，发现它的值是这样的`/run/user/1000/keyring-OOJ4q1/ssh`，之前没留意，突然想起当时查看auth.log时候貌似见过gnome-keyring的报告，根据这个线索又搜索了gnome-keyring，找到这么一篇文章
[https://wiki.gnome.org/Projects/GnomeKeyring/Ssh][1]，我的系统是ubuntu，工作的时候也是用图形界面，而gnome内置了一个ssh-agent，这个ssh-agent也会把~/.ssh下的*.pub文件加进去：

```
The SSH agent automatically loads files in ~/.ssh which have corresponding *.pub paired files. Additional SSH keys can be manually loaded and managed via the ssh-add command.
```
但还是不能解惑，因为我是靠手动添加文件才让builder的密钥生效的。

后来尝试了文字界面登录，默认没有开启ssh-agent，测试成功。

但还是有些不能理解的地方：

1. 即便是手动开启ssh-agent，也不会失败;
2. 还有ssh config的影响；
3. gnome-keyring为什么利用ssh-add加入文件才能工作；

因为解决这个问题花费的时间太长了，暂时记录下一些关键信息，等有空再一个个测试。

## ~/.ssh/config的影响

我的confg内容如下

```
Host *
     StrictHostKeyChecking no
     IdentityFile /home/bluven/.ssh/builder
```

在不开启ssh-agent的情况下，ssh只会尝试/home/bluven/.ssh/builder这个密钥，如果文件名改动，ssh会失败。
如果开启ssh-agent，并利用ssh-add加入改名后的密钥，ssh仍会成功。

## 启示

日志很重要，尤其是你不懂的东西

  [1]: https://wiki.gnome.org/Projects/GnomeKeyring/Ssh
