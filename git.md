## git

## 1.安装

```shell
sudo apt install git
```

## 2.基础

#### 1) 查看某个命令的帮助 例如查看commit命令

```shell
git help commit
```

<img src="http://qkwfs9p87.hn-bkt.clouddn.com/image-20201205144530970.png" alt="image-20201205144530970" style="zoom: 80%;" />

#### 2) 修改git的配置文件

```shell
git config -l
```

发现为空 则我们需要去设置

因为Git是分布式版本控制系统，所以，每个机器都必须自报家门：你的名字和Email地址。

```shell
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```

![image-20201205160412070](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201205160412070.png)

还需要配置以下换行符，因为windows上的换行符和mac和类unix不一样，在跨平台时就痛苦了，为了统一，可以将提交到仓库的换行符同配置成unix格式。

```shell
git config --global core.autocrlf input
```

给常用命令起个别名

```shell
git config --global alias.st status #git st
git config --global alias.co checkout #git co
git config --global alias.br branch #git br
git config --global alias.ci commit #git ci
```

![image-20201205160629850](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201205160629850.png)

如果想把git仓库提交到服务器，则可利用SSH，配置秘钥，实现免密登录

```
ssh-keygen -t rsa -C yanxuefeng@baidu.com # 生成秘钥

cat ~/.ssh/id_rsa.pub # 获取公钥，配置到github 的sshkey

ssh -T git@github.com # 测试是否生效
```

#### 3) 仓库

玩git，首先得有个仓库，获取仓库有两种方法

```shell
git init #在当前文件夹初始化一个仓库
```

```shell
git clone url #克隆一个已有仓库
```

#### 4）git的工作流

![](http://qkwfs9p87.hn-bkt.clouddn.com/486.png)

#### 5) git的文件状态

![](https://yanhaijing.com/blog/487.png)

### **Quick setup** — if you’ve done this kind of thing before



Get started by [creating a new file](https://github.com/ZeroAC/myblog/new/main) or [uploading an existing file](https://github.com/ZeroAC/myblog/upload). We recommend every repository include a [README](https://github.com/ZeroAC/myblog/new/main?readme=1), [LICENSE](https://github.com/ZeroAC/myblog/new/main?filename=LICENSE.md), and [.gitignore](https://github.com/ZeroAC/myblog/new/main?filename=.gitignore).

### …or create a new repository on the command line



```
echo "# myblog" >> README.md
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:ZeroAC/myblog.git
git push -u origin main
                
```

### …or push an existing repository from the command line



```
git remote add origin git@github.com:ZeroAC/myblog.git
git branch -M main
git push -u origin main
```

### …or import code from another repository

You can initialize this repository with code from a Subversion, Mercurial, or TFS project.

![image-20201205172833040](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201205172833040.png)



### 3.常用技巧记录

#### 1）清楚的看到最近的历史记录

![image-20201210210927325](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201210210927325.png)

