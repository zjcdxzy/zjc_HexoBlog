layout: '[post]'
title: cocoapods的安装和使用
date: 2015-11-30 20:15:04
categories: Tips
tags: 
---

- gem sources --remove `https://rubygems.org/`
- gem sources - a `http://ruby.taobao.org/`
- gem sources -l 
- mac执行命令 sudo gem install cocoapods 

如果安装仍然失败，使用https的淘宝的ruby源

- gem sources --remove `http://ruby.taobao.org/`
- gem sources - a `https://ruby.taobao.org/`

下图为安装成功

```
zjcs-MacBook-Pro:~ zjc$ sudo gem install cocoapods
Fetching: cocoapods-0.39.0.gem (100%)
Successfully installed cocoapods-0.39.0
Parsing documentation for cocoapods-0.39.0
Installing ri documentation for cocoapods-0.39.0
1 gem installed
```

- 使用的 pod setup ，直接下载podfile中对应的第三方库
- pod help 查看pod对应的命令如下

Usage:

```
    $ pod COMMAND

     CocoaPods, the Cocoa library package manager.
```
     

Commands:

``` bash

    + cache      Manipulate the CocoaPods cache
    + init       Generate a Podfile for the current directory.
    + install    Install project dependencies to Podfile.lock versions
    + ipc        Inter-process communication
    + lib        Develop pods
    + list       List pods
    + outdated   Show outdated project dependencies
    + plugins    Show available CocoaPods plugins
    + repo       Manage spec-repositories
    + search     Search for pods.
    + setup      Setup the CocoaPods environment
    + spec       Manage pod specs
    + trunk      Interact with the CocoaPods API (e.g. publishing new specs)
    + try        Try a Pod!
    + update     Update outdated project dependencies and create new
                 Podfile.lock

```

Options:

``` bash
    --silent     Show nothing
    --version    Show the version of the tool
    --verbose    Show more debugging information
    --no-ansi    Show output without ANSI codes
    --help       Show help banner of specified command

```

- MAC `CocoaPods`卸载命令  sudo gem uninstall cocoapods
