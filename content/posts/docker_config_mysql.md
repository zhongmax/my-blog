---
title: "docker配置mysql(5.7、8.0)"
date: 2024-02-18T23:16:42+08:00
---

### 下载镜像

```shell
# 8.0
docker pull mysql:8.0.35

# 5.7
docker pull mysql:5.7.44
```

### 启动命令

#### 5.7

创建文件夹

```shell
mkdir ~/data/mysql5.7 \
~/data/mysql5.7/log \
~/data/mysql5.7/data \
~/data/mysql5.7/conf.d \
~/data/mysql5.7/mysql-files
```

如果需要配置`/etc/my.cnf`文件，需要先启动一个测试的容器，然后将该文件通过`docker cp`拷贝出来再进行映射。

贴一份`5.7.44`的配置

```shell
[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mysql.conf.d/
```

`docker`命令

```shell
docker run -p 3306:3306 --name mysql5.7 \
-v /home/max/data/mysql5.7/log:/var/log/mysql \
-v /home/max/data/mysql5.7/data:/var/lib/mysql \
-v /home/max/data/mysql5.7/conf.d:/etc/mysql/conf.d \
-v /home/max/data/mysql5.7/mysql-files:/var/lib/mysql-files \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:5.7.44 \
--character-set-server=utf8mb4 \
--collation-server=utf8mb4_unicode_ci
```

#### 8.0

创建文件夹

```shell
mkdir ~/data/mysql8.0 \
~/data/mysql8.0/log \
~/data/mysql8.0/data \
~/data/mysql8.0/conf.d \
~/data/mysql8.0/mysql-files
```

8.0.35的`my.cnf`配置

```shell
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
# innodb_buffer_pool_size = 128M
#
# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
# log_bin
#
# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
# join_buffer_size = 128M
# sort_buffer_size = 2M
# read_rnd_buffer_size = 2M

# Remove leading # to revert to previous value for default_authentication_plugin,
# this will increase compatibility with older clients. For background, see:
# https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_default_authentication_plugin
# default-authentication-plugin=mysql_native_password
skip-host-cache
skip-name-resolve
datadir=/var/lib/mysql
socket=/var/run/mysqld/mysqld.sock
secure-file-priv=/var/lib/mysql-files
user=mysql

pid-file=/var/run/mysqld/mysqld.pid
[client]
socket=/var/run/mysqld/mysqld.sock

!includedir /etc/mysql/conf.d/
```

`docker`命令

```shell
docker run -p 3306:3306 --name mysql8.0 \
-v /home/max/data/mysql8.0/log:/var/log/mysql \
-v /home/max/data/mysql8.0/data:/var/lib/mysql \
-v /home/max/data/mysql8.0/conf.d:/etc/mysql/conf.d \
-v /home/max/data/mysql8.0/mysql-files:/var/lib/mysql-files \
-e MYSQL_ROOT_PASSWORD=root \
-d mysql:8.0.35 \
--character-set-server=utf8mb4 \
--collation-server=utf8mb4_unicode_ci
```
