# 查看 mysql的历史记录

```

cat /root/.mysql_history

```


# 改变mysql输出样式

```

prompt \u@root \r:\m:\s->


在my.cnf 配置文件[mysql] 中配置.  永久
[mysql]
prompt=\\u@root \\r:\\m:\\s->

```

# 远程登录

```

mysql -u root -p -h 192.22.22.22 -P3306


```

# 模糊show

```

show databases like 'my%';


```



# 查看主机名和权限

```

select user.host from mysql.user;


```


# SHOW GRANTS;

```

SHOW GRANTS;

root@root 04:11:14->SHOW GRANTS;

+---------------------------------------------------------------------+

| Grants for root@localhost  |

+---------------------------------------------------------------------+

| GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |

| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION |

+---------------------------------------------------------------------+


// 查看某个用户 的权限

show grants for 'root'@'localhost';

+---------------------------------------------------------------------+

| Grants for root@localhost  |

+---------------------------------------------------------------------+

| GRANT ALL PRIVILEGES ON *.* TO 'root'@'localhost' WITH GRANT OPTION |

| GRANT PROXY ON ''@'' TO 'root'@'localhost' WITH GRANT OPTION |

+---------------------------------------------------------------------+


添加codetom为超级管理员. 等价root
grants all

GRANT ALL PRIVILEGES ON *.* TO 'codetom'@'localhost' WITH GRANT OPTION

GRANT ALL PRIVILEGES ON *.* TO 'codetom'@'%' WITH GRANT OPTION

```


# 加密码

```

mysqladmin -u root password 123456a

//改密码
mysqladmin -u root -p '123456a' password 'qazwsx'



// mysql 里面修改密码

UPDATE mysql.user SET password=PASSWORD("qwerty") WHERE user='root';
flush PRIVILEGES;


```




# 刷新提交

```

flush PRIVILEGES


```



# 忘记 密码然后忽略验证登录进去修改密码

```

/etc/init.d/mysqld stop

mysqld_safe --skip-grant-tables --user=mysql &

mysql

UPDATE mysql.user SET password=PASSWORD("abc") WHERE user='root';

flush PRIVILEGES;

quit;

mysqladmin -uroot -pabc shutdown

/etc/init.d/mysqld start
```


# 查看mysql启动方式

```

➜  /wt ps -ef|grep mysql
root     21441 20626  0 16:35 pts/0    00:00:00 /bin/sh /application/mysql/bin/mysqld_safe --skip-grant-tables --user=mysql
mysql    21682 21441  0 16:35 pts/0    00:00:00 /application/mysql/bin/mysqld --basedir=/application/mysql --datadir=/application/mysql/data --plugin-dir=/application/mysql/lib/plugin --user=mysql --skip-grant-tables --log-error=sanfan.err --pid-file=sanfan.pid --socket=/tmp/mysql.sock --port=3306


```


# 密码登录

```

mysql -u root -p


```


# 查看库的建表语句

```

mysql> show create database mysql\G

*************************** 1\. row ***************************

 Database: mysql

Create Database: CREATE DATABASE `mysql` /*!40100 DEFAULT CHARACTER SET latin1 */

1 row in set (0.01 sec)



```

# 编译的时候没有指定字符集或者指定了和程序不同的字符集,如何解决..  指定字符集创建 数据库即可

```

create database wutong_utf8 CHARACTER SET utf8 COLLATE utf8_general_ci;


create database wutong_utf8 CHARACTER SET gbk COLLATE gbk_general_ci;

mysql> show create database wutong_utf8\G

*************************** 1\. row ***************************

 Database: wutong_utf8

Create Database: CREATE DATABASE `wutong_utf8` /*!40100 DEFAULT CHARACTER SET utf8 */

1 row in set (0.00 sec)


```



# 进入数据库

```

use mysql;


```


# 显示当前数据库


```

mysql> select database();

+------------+

| database() |

+------------+

| mysql |

+------------+

1 row in set (0.00 sec)

```


# 删库跑路

```

drop database wutg_utf8;

```


# 查看用户

```

mysql> select user,host from mysql.user;

+---------+-----------+

| user | host |

+---------+-----------+

| root | 127.0.0.1 |

| root | ::1  |

|  | localhost |

| codetom | localhost |

| root | localhost |

|  | sanfan |

| root | sanfan |

+---------+-----------+

7 rows in set (0.00 sec)


```


# 删除多余的用户

```
// 语法
drop  user "user"@"主机域";


mysql> drop user "codetom"@"localhost";

Query OK, 0 rows affected (0.01 sec)


如果的drop 删除不了(一般是特殊字符或大写), 可以哦用下面

delete from mysql.user where user='root' and host='name';
flush PRIVILEGES;
```


# 创建用户并授权

```

CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'mypass';

GRANT ALL ON db1.* TO 'jeffrey'@'localhost';

GRANT SELECT ON db2.invoice TO 'jeffrey'@'localhost';

GRANT USAGE ON *.* TO 'jeffrey'@'localhost' WITH MAX_QUERIES_PER_HOUR 90;



// 1. 创建用户 + 授权
// 创建用户
CREATE USER 'jeffrey'@'localhost' IDENTIFIED BY 'mypass';
// 授权
GRANT ALL ON db1.* TO 'jeffrey'@'localhost';


//  2. 上面两条相当于下面一条
GRANT ALL ON db1.* TO 'jeffrey'@'localhost' IDENTIFIED BY 'mypass';


GRANT: 授权命令
all privileges: 对应权限
on dbname.* : 目标:库和表
to username@'localhost': 用户名和客户端主机
IDENTIFIED BY 'mypass': 用户密码


// 3. 创建一个doukeyi用户密码是123456a7, 从哪里 都可以登录, 所有库所有表都可以访问
grant all on *.* to 'doukeyi'@'%' identified by '123456a7'

grant all on *.* to 'mytest1'@'111.194.40.169' identified by '123456a78';
```


```




mysql -u root -p

use mysql;

update user set host = '%'  where user = 'root';




select host from user where user = 'root';

# mysql> select host,user from user where user='root';






==================

一般情况下
mysql -u root -p
mysql>use mysql;
mysql>select 'host' from user where user='root';
mysql>update user set host = '%' where user ='root';
mysql>flush privileges;
mysql>select 'host'   from user where user='root';




# RROR 1062 (23000): Duplicate entry '%-root' for key 'PRIMARY' 

如果执行update语句时出现ERROR 1062 (23000): Duplicate entry '%-root' for key 'PRIMARY' 错误，说明有多个ROOT用户纪录在USER表中了.
需要select host from user where user = 'root';
查看一下host是否已经有了%这个值，有了就可以了.
mysql> select host,user from user where user='root';


```

# 刚刚安装完成试试如下命令

```

mysql -uroot -p

```
没有密码直接进去了

没有密码直接回车就进去了
#  加密码

```

mysqladmin -u root password '1'23456

```

# 密码登录

```

mysql -uroot -p123456


```


# 有密码了怎么改密码了?如下

```

mysqladmin -uroot -p123456a password 1234567a

mysql -u root -p

```


# 带密码的登录像上面那样输入登录会有记录,  怎么解决? 如下

清空
```

history -c 


history -d


```


