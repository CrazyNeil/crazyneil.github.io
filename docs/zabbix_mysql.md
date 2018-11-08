**[首页](https://docs.workwithme.vip)** > zabbix 基于自带模板的MYSQL监控指南

### 1. 创建MYSQL账号

    该账号用于agent获取被监控的MYSQL状态信息

```sql
create user 'zabbix'@'localhost' IDENTIFIED BY 'your-password';
GRANT USAGE ON *.* TO 'zabbix'@'localhost' IDENTIFIED BY 'your-password';
flush privileges;
```

### 2. 创建 agent 配置文件

```bash

vim /etc/zabbix/zabbix_agentd.conf.d/userparameter_mysql.conf

#内容如下

# For all the following commands HOME should be set to the directory that has .my.cnf file with password information.
#
# Flexible parameter to grab global variables. On the frontend side, use keys like mysql.status[Com_insert].
# Key syntax is mysql.status[variable].
UserParameter=mysql.status[*],echo "show global status where Variable_name='$1';" | HOME=/etc/zabbix mysql -N | awk '{print $$2}' # My line
#
# Flexible parameter to determine database or table size. On the frontend side, use keys like mysql.size[zabbix,history,data].
# Key syntax is mysql.size[<database>,<table>,<type>].
# Database may be a database name or "all". Default is "all".
# Table may be a table name or "all". Default is "all".
# Type may be "data", "index", "free" or "both". Both is a sum of data and index. Default is "both".
# Database is mandatory if a table is specified. Type may be specified always.
# Returns value in bytes.
# 'sum' on data_length or index_length alone needed when we are getting this information for whole database instead of a single table
UserParameter=mysql.size[*],echo "select sum($(case "$3" in both|"") echo "data_length+index_length";; data|index) echo "$3_length";; free) echo "data_free";; esac)) from information_schema.tables$([[$
#
#Default below
UserParameter=mysql.ping,HOME=/etc/zabbix mysqladmin ping | grep -c alive 
#
#My line
UserParameter=mysql.uptime,HOME=/etc/zabbix mysqladmin status | cut -f2 -d ":" | cut -f1 -d "T" | tr -d " "
UserParameter=mysql.threads,HOME=/etc/zabbix mysqladmin status | cut -f3 -d ":" | cut -f1 -d "Q" | tr -d " "
UserParameter=mysql.questions,HOME=/etc/zabbix mysqladmin status | cut -f4 -d ":"|cut -f1 -d "S" | tr -d " "
UserParameter=mysql.slowqueries,HOME=/etc/zabbix mysqladmin status | cut -f5 -d ":" | cut -f1 -d "O" | tr -d " "
UserParameter=mysql.qps,HOME=/etc/zabbix mysqladmin status | cut -f9 -d ":" | tr -d " "
UserParameter=mysql.version,mysql -V

```

### 3. 创建 agent 与 mysql 的连接文件

```bash

vim /etc/zabbix/.my.cnf

# 内容如下
[mysql]
user=zabbix
password=your-password
[mysqladmin]
user=zabbix
password=your-password

```

### 4. 重启 agent 

```bash

sudo service zabbix-agent restart

```

### 5. 在 agent server 中进行模板的设置和构图