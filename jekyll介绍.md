## jekyll使用

### 一、介绍       

**Jekyll** 是一个简单的博客形态的静态站点生产机器。它有一个模版目录，其中包含原始文本格式的文档，通过一个转换器（如 [Markdown](http://daringfireball.net/projects/markdown/)）和我们的 [Liquid](https://github.com/Shopify/liquid/wiki) 渲染器转化成一个完整的可发布的静态网站，你可以发布在任何你喜爱的服务器上。Jekyll 也可以运行在 [GitHub Page](http://pages.github.com/) 上，也就是说，你可以使用 GitHub 的服务来搭建你的项目页面、博客或者网站，而且是**完全免费**的。

### 二、安装

#### 1. 安装依赖项

```shell
sudo apt-get install ruby-full build-essential zlib1g-dev
```

#### 2.环境变量配置

即设置一个gem安装目录。以下命令把环境变量添加到`~/.bashrc`文件中，以配置gem的安装路径.

```shell
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

#### 3. 安装`jekyll`和`Bundler`

   ```shell
gem install jekyll bundler
   ```

   时间有点久，耐心等待下...

## 三、使用指南

### 1.快速体验

在当前文件夹下安装一个博客

```shell
sudo jekyll new myblog
```

耐心等待一下,等待安装成功后 进入博客文件夹

```shell
cd myblog
```

构建网站并启动服务

```shell
bundle exec jekyll serve
```

在浏览器中输入` http://localhost:4000`即可体验

### 2.入门

[官方教程](https://jekyllrb.com/docs/)



