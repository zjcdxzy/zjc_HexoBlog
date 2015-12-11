layout: '[post]'
title: Hexo 的安装和配置
date: 2015-11-30 14:26:55
categories: Tips
tags: Hexo 
---
一、 安装git
启动mac终端，在终端中执行命令，查看git是否安装

``` bash
git -- version
```

查看执行的结果:

``` bash
zjcs-MacBook-Pro:zjc_HexoBlog zjc$ git --version
git version 2.1.3.36.g8e36a6d
```

 	
如果mac系统中没有安装git，启动MAC终端，在终端中执行命令

``` bash
ruby -e "$(curl -fsSL   https://raw.githubusercontent.com/Homebrew/install/master/install)"   
brew install git
```


 
二、安装Node.js
启动终端查看node是否安装 
``` bash   
node --version 
```
    
    
对应的结果：

``` bash
zjcs-MacBook-Pro:zjc_HexoBlog zjc$ node --version
v0.12.2
```

如果MAC系统中没有安装Node，在终端执行命令：

nvm (Node Version Manager)，详见官方介绍：[nvm](https://github.com/creationix/nvm#node-version-manager)
 
``` bash
git clone git://github.com/creationix/nvm.git ~/.nvm
cd /Users/dev.temobi/.nvm  （备注：/Users/dev.temobi/.nvm 是执行上一条命令的结果显示.nvm所在的路径）
sh install.sh
```

或者直接去Node.js[官网](https://nodejs.org/en/#download)下载软件，然后安装到mac电脑上

三、 安装Hexo

- 在终端中执行命令 `npm install -g hexo `

- 上述命令在安装成功后，出现错误信息：

```	bash 
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```

- 网上搜索解决办法，建议安装hexo时使用命令为：


`$ npm install hexo --no-optional`

- cd 到指定的文件夹中创建  hexo 

``` bash
hexo init 
npm install 
```

-  完成后如果无错误，则说明已经搭建起本地的hexo博客了
执行命令

``` bash
hexo generate
hexo server
```

然后到浏览器输入 `http://0.0.0.0:4000/` 就可以预览博客了

四、部署hexo博客到github

- 自己的github账号中创建Repository ,名称和GitHub账号相匹配

> zjcdxzy.github.io
> 
> 获取自己的Repository地址https://github.com/zjcdxzy/zjcdxzy.github.io.git



- 修改_config.yml

1. 找到

	```bash
	deploy： 
	type:
	```

2. 修改为：

	``` bash
	deploy:
	type:git
	repo:https://github.com/zjcdxzy/zjcdxzy.github.io.git
	```
	保存退出

-  github ssh 部署,生成 SSH keys 

 ```
ssh-keygen -t rsa -C "zjcdxzy@hotmail.com"
ssh-agent -s
ssh-add ~/.ssh/id_rsa
```

- 把SSH keys 添加到github账户中：
![hexo_sshKey.png](http://7xosar.com1.z0.glb.clouddn.com/hexo_sshKey.png)

1. 前往~/.ssh 文件夹中，打开id_rsa.pub。

2. 复制其中的内容，然后粘贴到github账号下设置中增加SSH keys的区域。

3. 通过`ssh -T git@github.com` 命令查看SSH keys 是否添加成功。

4. 执行结果如下，则说明安装成功。

	``` bash
	localhost:~ zjc$ ssh -T git@github.com
	Hi zjcdxzy! You've successfully authenticated, but GitHub does not provide shell access.
	```

- 部署Hexo,在终端中执行命令

```
hexo generate
hexo deploy
```
      
1. 结果出现错误


	`ERROR Deployer not found: git`
	
	
	查找解决办法：http://hexo.io/docs/deployment.html

2. 执行npm命令修改


   `npm install hexo-deployer-git --save`
     
     
3. 执行 hexo deploy 最终部署完成。
访问[zjcdxzy的blog](http://zjcdxzy.github.io)

4. Hexo常用命令列表

		简写命令| 原命令
		------------- | -------------
		 jianxi  |  hexo generate
		 hexo    d | hexo deploy
		 hexo    s | hexo server
		 hexo    n | hexo new



*参考文章*：http://www.aips.me/hexo-independent-blog-new-ways.html

*使用的主题*：https://github.com/tufu9441/maupassant-hexo