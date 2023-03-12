# 安装与使用

 

 

## Ubuntu安装

Ubuntu 可以使用 apt-get 安装 PostgreSQL：

```
sudo apt-get update
sudo apt-get install postgresql postgresql-client
```

安装完毕后，系统会创建一个数据库超级用户 postgres，密码为空。

```
#  sudo -i -u postgres
```

这时使用以下命令进入 postgres，输出以下信息，说明安装成功：

```
~$ psql
psql (9.5.17)
Type "help" for help.

postgres=# 
```

输入以下命令退出 PostgreSQL 提示符：

```
\q
```

PostgreSQL 安装完成后默认是已经启动的，但是也可以通过下面的方式来手动启动服务。

```
sudo /etc/init.d/postgresql start   # 开启
sudo /etc/init.d/postgresql stop    # 关闭
sudo /etc/init.d/postgresql restart # 重启
```