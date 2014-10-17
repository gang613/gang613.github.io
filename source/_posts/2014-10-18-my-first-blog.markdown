---
layout: post
title: " 如何将GitHub Pages设置为个人Blog "
date: 2014-10-18 00:31:30 +0800
comments: true
categories: 
---
本文介绍如何采用git+github+Octopress搭建blog，Github Pages采用 User & Organization Pages 类型，本地环境为Ubuntu 14.04 LTS 。

##注册GitHub账户，设置Repository
访问[https://github.com](https://github.com) ,注册用户。
创建新的Repository，名称为"username.github.io",将来个人blog地址为http://username.github.io ，其中username是注册用户或组织名。

##客户端git安装
安装git，两种方法。  
方法一：采用apt

    $ sudo apt-get install git-core

方法二：采用源码安装

    $ sudo apt-get update  
    $ sudo apt-get install libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev build-essential
    $ wget https://code.google.com/p/git-core/downloads/detail?name=git-1.9.0.tar.gz
    $ tar -zxf git-1.9.0.tar.gz
    $ cd git-1.9.0
    $ make prefix=/usr/local all
    $ sudo make prefix=/usr/local install

##客户端git设置
设置git config  

    $ git config --global user.name '用户名'
    $ git config --global user.email '邮件地址'

    #查看设置
    $ git config --list

设置ssh keys

    //备份
    $ cd ~/.ssh
    $ mkdir key_backup
    $ cp id_rsa* key_backup
    $ rm id_rsa*
    #生成新的ssh key
    $ ssh-keygen -t rsa -C gang613@126.com
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/your_user_directory/.ssh/id_rsa):<回车就好>
    #输入加密串
    Enter passphrase (empty for no passphrase):<输入加密串>
    Enter same passphrase again:<再次输入加密串>

添加ssh key到GitHub

    $ gedit ~/.ssh/id_rsa.pub

复制文件内容，在GitHub主页Settings页面，选择SSH Keys，将复制内容粘帖进去，点击`Add Key`即可。

测试

    $ ssh -T git@github.com
    The authenticity of host 'github.com (207.97.227.239)' can't be established.
    RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
    Are you sure you want to continue connecting (yes/no)?
    #输入yes
    Hi Username! You've successfully authenticated, but GitHub does not provide shell access.

##安装Ruby环境

创建脚本install-ruby.sh

    $ vi install-ruby.sh

输入以下内容

    #!/usr/bin/env bash
    apt-get -y update
    apt-get -y install build-essential zlib1g-dev libssl-dev libxml2-dev libxslt-dev libreadline6-dev libyaml-dev
    # apt-get -y install libmysqlclient-dev # uncomment for mysql support
    cd /tmp
    wget http://ftp.ruby-lang.org/pub/ruby/2.1/ruby-2.1.3.tar.gz
    tar -xvzf ruby-2.1.3.tar.gz
    cd ruby-2.1.3/
    ./configure --prefix=/usr/local
    make
    make install

保存推出后，执行

    $ sudo ./install-ruby.sh

##安装rbenv

rbenv 是管理ruby版本依赖的工具，类似于RVM，安装Octopress时需要使用。先运行以下脚本：

    $ sudo curl  https://raw.githubusercontent.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash

修改bashrc文件

    $ vi ~/.bashrc

增加以下内容：

    export RBENV_ROOT="${HOME}/.rbenv"
    if [ -d "${RBENV_ROOT}" ]; then
        export PATH="${RBENV_ROOT}/bin:${PATH}"
        eval "$(rbenv init -)"
    fi

使bashrc生效

    $ source ~/.bashrc

##更新gem源
gem默认源为 https://rubygems.org  ,比较慢，先更新为淘宝源
查看当前有的源

    $ gem sources -l

移除https://rubygems.org源

    $ gem sources --remove https://rubygems.org/

增加http://ruby.taobao.org 

    $ gem sources -a http://ruby.taobao.org/

添加完用gem sources -l再查看一下，请确保只有http://ruby.taobao.org/

##安装Octopress

先clone源码

    $ git clone git://github.com/imathis/octopress.git octopress
    $ cd octopress  

安装依赖

    $ sudo gem install bundler
    $ rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
    $ bundle install

安装默认的Octopress 主题

    $ rake install

##安装NodeJS
如果不安装nodejs，可能在后续rake preview时出现"Could not find a JavaScript runtime"错误。

    $ sudo apt-get install nodejs

##Octopress配置

Octopress配置主要修改_config.yml文件，关于_config.yml文件中的更多内容，请看这里的内容：[Configuring Octopress](http://octopress.org/docs/configuring/)

创建新blog

    $ rake new_post["title"]
新文件创建于 _posts目录下  
生成

    $ rake generate
预览

    $ rake preview   

配置octopress与github的连接：


## Reference

[1]. [How To Install Git on Ubuntu 12.04](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-12-04)  
[2]. [使用Github Pages建独立博客](http://beiyuu.com/github-pages/)  
[3]. [Install Ruby 1.9.3-p286 on Ubuntu 12.04 LTS](https://gist.github.com/bartimaeus/2845824)  
[4]. [How To Install Ruby On Rails on Ubuntu 12.04 LTS with rbenv](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-on-ubuntu-12-04-lts-with-rbenv--2)  
[5]. [更换Gem源](http://42.96.192.22/?cat=478)  
[6]. [使用Octopress搭建个人博客](http://sonnewilling.com/blog/2013/11/14/shi-yong-octopressda-jian-ge-ren-bo-ke/)  
