---
title: 使用Hexo、Github Pages和Appveyor搭建你的私人博客
date: 2018-08-19
modified: 2018-11-20
tags:
- hexo
- github
- appveyor
---





## 0. 写在前面

	查编程、算法相关资料时，经常看到很多挂载在精美的私人博客上的文章。博主过去的文章都是挂在简书上，虽然满足了撰文的基本需求，但订制属性不佳——作为一名不会按时吃药的coder，这当然是不能容忍的啦^-^
	
	闲话少叙，本博客的第一篇文章会详细介绍搭建私人博客的三个组件和基本步骤：

> Hexo: 博客框架，用于快速高效生成博客所需的前台代码
>
> Github Pages: 免费的博客托管处（有访问量、总大小限制）
>
> Appveyor:（可选） 在线持续集成工具，从此你只需做写文、git上传两件事

## 1. Hexo

### 1.1 Hexo是什么

Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown(或其他渲染引擎)解析文章，在几秒内，即可利用靓丽的主题生成静态网页。——[Hexo官方文档](https://hexo.io/zh-cn/docs/index.html)

简单来说，Hexo就是帮助我们完成前台HTML生成的工具，让我们只需要专注于Markdown文档的编写。

### 1.2 安装Hexo

安装前需要保证系统预装[Nodejs](https://nodejs.org/zh-cn/)和[Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)，之后可进行[Hexo安装](https://hexo.io/zh-cn/docs/index.html)，以linux下的安装为例：

```bash
npm install -g hexo-cli
```

这里有两点需要留意：

1. npm的默认repo有可能不好用，如果下载过程缓慢或无法启动下载，请更换为taobao的repo：

```bash
npm config set registry https://registry.npm.taobao.org
```

2. 如果你处于公司等需要代理连接的内部网络，记得给npm配置proxy：

```bash
npm config set proxy http://your.proxy.server:port
```

### 1.3 Hexo的Hello World

让我们以一个Hello World工程作为Hexo之旅的第一站，在**本地环境**使用Hexo生成博客内容并查看总共分三步：初始化Hexo工作目录，写文章/生成内容，启动博客后台：

#### 1.3.1 初始化

```bash
mkdir hexo_blog
hexo init hexo_blog
```

这一步会在指定的文件夹hexo_blog下下载建站所需的文件，下载过程需要花费一段时间，要耐心等待。

#### 1.3.2 写文章/生成内容

```bash
cd hexo_blog
hexo new post hello-world
```

这一步会在source/_posts/下生成一个名为hello-world.md的文件，你可以在里面编辑任意内容，完成后生成前台所有代码：

```bash
hexo generate # 或 hexo g
```

generate命令会生成博客前台所需的一切（HTML, CSS, JS等等）

#### 1.3.3 启动本地server

```bash
hexo server # 或 hexo s
```

此步骤默认会完成建站并在本地4000端口启动server，打开浏览器输入localhost:4000，你就能看到自己的博客啦

## 2. Github Pages

简单说，Github Pages是Github为用户提供的用于托管个人静态网站的服务，免去了我们自己买VPS搭环境的麻烦，只需要免费注册的github账号并新建一个特定名称的项目，我们就可以获得这样一个服务了：

**登陆你的github首页，选择New Repositories，项目名"username.github.io"，用你的github用户名替换username**

工程创建成功，你的Github Pages服务也就有了，下一步就是在你的hexo工作路径与Github Pages之间建立关联，回到小节1的hexo_blog目录下，编辑全局配置文件_config.yml，将deploy相关内容修改成如下模样：

```yaml
deploy:
  type: git
  repo: https://github.com/username/username.github.io.git
  branch: master
```

仍要记得以你的用户名替换username，接下来就是deploy过程了，这一步会将你的博客内容上传到Github Pages服务上，不过在此之前，你需要装一个hexo部署到github上的插件：

```bash
npm install hexo-deployer-git --save
```

准备好，博客要起飞啦

```bash
hexo deploy
```

如无意外，现在输入username.github.io，你的个人博客和你首次见面了！

这以后，你的工作流就是：

```bash
# edit your markdown file(s)...
hexo clean
hexo generate
hexo deploy
```

## 3. Appveyor

折腾几轮，新增了几篇博客，是不是新鲜感满满？相信我，这和开手动挡汽车一样，对于重复工作的厌烦总有一天会来到。另外，这种本地生成、上传内容的工作方式还有个问题，当你更换工作环境时，需要提前拷贝hexo目录——Github工程的master分支，和你的工作环境并不一样哦。

为解决上述两个问题，我们引入Appveyor（Travis也一样）作为持续集成工具。持续集成，简单说就是个web服务，它会一直监听某种触发信号，当监听到信号时，服务会按照提前设定好的逻辑执行一系列动作。在当前场景中，持续集成用于监听hexo源码的修改（可以把hexo工作路径作为新的github工程，以该工程的更新作为信号），并执行生成、部署活动。

首先，你需要把小节1的工作路径，即hexo_blog上传到新的git repo上，为了便于管理，我没有新建git工程，而是在username.github.io工程上新建了分支：



根目录下要添加appveyor的hook，为的是在appveyor和此目录建立关联：



你需要登陆Appveyor官网，注册账号



