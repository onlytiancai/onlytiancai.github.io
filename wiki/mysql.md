centos

### 安装mysql

**yum 安装**

    yum list installed | grep mysql
    yum -y remove mysql-libs.x86_64
    yum list | grep mysql
    yum -y install mysql-server mysql mysql-devel
    rpm -qi mysql-server
    vi /etc/my.cnf
        default-character-set=utf8
    service mysqld start
    chkconfig --list | grep mysql*
    chkconfig -add mysqld
    ps -ef | grep mysql

**设置mysql**

    mysqladmin -u root password 123456
    mysql -uroot -p

**忘记密码，修改密码**

    service mysqld stop
    mysqld_safe --user=root --skip-grant-tables
    mysql -uroot -p

        update user set password=password("password") where user="root";
        flush privileges; 

    mysqladmin shutdown -p
    service mysqld start

**远程访问**

开放防火墙的端口号
mysql增加权限：mysql库中的user表新增一条记录host为“%”，user为“root”。

**相关目录**

数据库目录
/var/lib/mysql/
配置文件
/usr/share/mysql（mysql.server命令及配置文件）
相关命令
/usr/bin（mysqladmin mysqldump等命令）
启动脚本
/etc/rc.d/init.d/（启动脚本文件mysql的目录）

### 单机多实例

**创建目录，设置权限**

    mkdir -p /data/mysql/
    cd /data/mysql/
    mkdir -pv {3307,3308}/data
    chown -R mysql.mysql ./

**配置启动多实例**

    cd /usr/share/mysql
    mysql_install_db --user=mysql --basedir=/usr/ --datadir=/data/mysql/3307/data
    mysql_install_db --user=mysql --basedir=/usr/ --datadir=/data/mysql/3308/data

    mysqld_multi --example > /data/mysql/multi.cnf
    vi /data/mysql/multi.cnf

        [mysqld_multi]
        mysqld     = /usr/bin/mysqld_safe
        mysqladmin = /usr/bin/mysqladmin
        user       = root 
        password   = password 

        [mysqld1]
        socket     = /tmp/mysql.sock1
        port       = 3307
        pid-file   = /data/mysql/3307/mysql.pid
        datadir    = /data/mysql/3307/data

        [mysqld2]
        socket     = /tmp/mysql.sock2
        port       = 3308
        pid-file   = /data/mysql/3308/mysql.pid
        datadir    = /data/mysql/3308/data

    mysqld_multi --defaults-file=/data/mysql/multi.cnf start 1,2
    tail -n 50 /usr/share/mysqld_multi.log
    ps -ef | grep mysql
    netstat -tnpl

    mysql -h127.0.0.1 -P3307
    mysql -h127.0.0.1 -P3308
    mysql -S /tmp/mysql.sock1
    mysql -S /tmp/mysql.sock2

**关闭实例**

    mysqladmin -S /tmp/mysql.sock1 shutdown

**设置密码**

    mysqladmin -uroot -S /tmp/mysql.sock2 password "123456"
    mysqladmin -uroot -S /tmp/mysql.sock1 password "password"

### 小提示

sql命令行下插入待`\`字符时，要把`\`替换成`\\`再插入。

    insert into t values(""\\u5317\\u4eac\\u5eb7\\u76db\\u65b0\\u521b\\u79d1\\u6280\\u6709\\u9650\\u516c\\u53f8"");

### 参考链接：

Centos使用yum安装mysql: http://chenguixian.blog.51cto.com/1646030/1700381
mysql多实例: http://amyhehe.blog.51cto.com/9406021/1696975
MySQL复制详解:http://amyhehe.blog.51cto.com/9406021/1699168
MySQL 5.5.35 单机多实例配置详解: http://freeloda.blog.51cto.com/2033581/1349312

