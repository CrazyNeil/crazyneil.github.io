**[首页](https://docs.workwithme.vip)** > zabbix 简明安装

    本文描述的安装方法是基于 Ubuntu 16.04 LTS 操作系统

### 1. 安装 Zabbix Server

    server 是核心组件，用于收集、存储、计算从目标机器获取（或接收）的 DataSet

```bash

sudo su # 提权一下
wget https://repo.zabbix.com/zabbix/4.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_4.0-2+xenial_all.deb
dpkg -i zabbix-release_4.0-2+xenial_all.deb
apt update

```

### 2. 安装前端组件

```bash

# 安装MYSQL组件集合，其中包含了MYSQL
apt install zabbix-server-mysql

# 给 MYSQL 设置 root 密码，省略
# 给 MYSQL 创建 zabbix 用户，切记！这个操作是必须的 ！！！具体SQL省略

# 安装 proxy 的 MYSQL组件， proxy 是 server 的一个服务组件，不是必须的，组件包括了：server / proxy / agent 
apt install zabbix-proxy-mysql

# 安装PHP WEB应用程序
apt install zabbix-frontend-php

# 编辑配置文件
vim /etc/apache2/conf-enabled/zabbix.conf

php_value max_execution_time 300
php_value memory_limit 128M
php_value post_max_size 16M
php_value upload_max_filesize 2M
php_value max_input_time 300
php_value max_input_vars 10000
php_value always_populate_raw_post_data -1
# 特别提醒，时区一定要设置，否则浏览器安装环节会提示你设置时区
php_value date.timezone Europe/Riga

# 重启 apache2 
service apache2 restart

# 解压SQL文件

cd /usr/share/doc/zabbix-server-mysql
gzip -d create.sql.gz

# 导入sql 文件
mysql -uroot -p
create database zabbix defautl character set utf8; # 创建 zabbix 数据库
exit;

mysql -uroot -p zabbix < create.sql

# 编辑配置文件

vim /etc/zabbix/zabbix_server.conf

# 注意把以下几项写入配置文件
DBHost=localhost
DBName=zabbix
DBUser=zabbix
DBPassword=<password>

# 完事了，启动 server 
service zabbix-server start
update-rc.d zabbix-server enable # 自动启动



访问：http://your-server-ip/zabbix  执行web安装进程
安装完毕后默认账号：Admin 密码：zabbix 登陆进行后续操作

```

### 4. Agent 代理安装

```bash
# 安装代理
apt install zabbix-agent
# 编辑配置文件
vim /etc/zabbix/zabbix_agentd.conf

Server=your-zabbix-server-ip-addr
ServerActive=your-zabbix-server-ip-addr
Hostname=YOUR SERVER NAME # 在 zabbix server 中添加host的时候注意填写当前配置的主机名

# 启动代理
service zabbix-agent start

# 注意打开 10050 默认端口，无论是 Server 还是 agent 所在的机器

```

### 4. 参考文档

- [应用商店](https://share.zabbix.com)
- [官方手册](https://www.zabbix.com/manuals)