---
layout: post
title: ubuntu server 18.04生产环境ruby on rails配置
date: 2020-07-29
summary: ubuntu ruby ruby_on_rails
categories: work
---

# ubuntu server 服务环境配置

## 基本配置

1. 更新系统

ubuntu 默认软件源为`archive.ubuntu.com` 阿里云或者其他云有些会用自己的 mirror 源速度比较快 无需修改 如果没有用这些源可以更改为 163 软件源

方法如下：
打开配置文件
`vim /etc/apt/source.list`
替换 163 源
`:%s/archive.ubuntu/mirrors.163/g`
更新系统
`apt update`
升级系统
`apt upgrade`
移除多余的软件包
`apt autoremove`
重启服务器
`reboot`

2. 用户配置
   新建 deployer 用户
   `useradd -d -s /bin/bash deployer`
   修改用户密码
   `passwd deployer`
   修改用户 sudo 权限
   `vim /etc/sudoers`
   增加一行
   `deployer ALL=(ALL:ALL) ALL`

3. 后续用 deployer 用户登录服务器 root 用户不使用

## 安装 mongodb

1. 配置软件源
   `wget -qO - https://www.mongodb.org/static/pgp/server-4.2.asc | sudo apt-key add -`
   使用 163 镜像加速安装
   `echo "deb [ arch=amd64 ] https://mirrors.163.com/mongodb/apt/ubuntu bionic/mongodb-org/4.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.2.list`
2. 安装 mongodb
   `sudo apt update`
   `sudo apt install mongodb-org`
3. 配置开机启动
   `sudo systemctl start mongod`

## 安装 redis

1. 下载 redis
   `wget http://download.redis.io/releases/redis-5.0.8.tar.gz`
   `tar xzf redis-5.0.8.tar.gz`
   `cd redis-5.0.8`
2. 编译安装
   `make`
   `sudo make install`
3. 安装服务开机启动
   `sudo utils/install_server.sh`
   都选默认即可

## 安装 memcached nginx

`sudo apt install memcached nginx`

## 安装 ruby 环境

1. 安装 rvm
   `gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB`
   `curl -sSL https://get.rvm.io | bash -s stable`
2. 配置 rvm 安装 mirror
   `echo "ruby_url=https://cache.ruby-china.com/pub/ruby" > ~/.rvm/user/db`
   `source /home/deployer/.rvm/scripts/rvm`
3. 安装 ruby
   `rvm install 2.7.0 --disable-binary`

## 参考文章

https://ruby-china.org/wiki/ruby-mirror

https://rvm.io/

https://docs.mongodb.com/manual/tutorial/install-mongodb-on-ubuntu/

https://redis.io/download

http://mirrors.163.com/
