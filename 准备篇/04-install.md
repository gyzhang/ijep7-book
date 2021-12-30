# 第4章 安装部署

iJEP 7 是一个典型的前后端分离微服务应用：

- 开发数据库使用 MySQL；
- 后端服务依赖于 Redis 和 Elasticsearch；
- 服务注册与发现中心使用 Consul；
- 前端采用 Vue + Element，部署于 Nginx 之上。

## 4.1 开发环境

开发环境通常采用 Windows，以下我们以 Windows 10 为例，详细介绍 iJEP 7 开发环境的安装配置。

开发环境以最小配置为宜，方便单机开发。

### 4.1.1 安装配置 JDK

首先去 Oracle 官网下载[ Java SE Development Kit 8u311](https://www.oracle.com/java/technologies/downloads/#java8)，然后双击运行下载回来的**jdk-8u311-windows-x64.exe**文件。

指定安装路径，例如，我将 JDK 安装到 **C:\\Java\\jdk1.8.0_311\\** 下。

> 需要注意的是，JDK安装目录尽量不要含中文和（或）空格。

安装完成 JDK 后需要设置 JAVA_HOME 环境变量为 C:\\Java\\jdk1.8.0_311\\。

将 JAVA_HOME\\bin 加入path，方面后续通过命令行使用 JDK。

验证 JDK 是否正确安装：打开命令窗口，输入 `java -version`，查看安装的 JDK 版本信息，如正确显示 JDK 版本信息，则 JDK 成功安装并配置正确。

### 4.1.2 安装配置 Redis

在 [https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases/download/win-3.2.100/Redis-x64-3.2.100.zip)  这里下载 Windows 预编译版本的 Redis，为了简便起见，我们选择解压包 Redis-x64-3.2.100.zip 文件。

安装测试文档，请参照[ Redis 简介](https://xprogrammer.net/wiki/1349284033200160/1349646836303552)。

下载后，将其解压到用户目录中，例如 `C:\Users\Kevin\iJEP7\run-env\Redis-x64-3.2.100`。

双击 `redis-server.exe` 运行 Redis 服务器，查看 Redis 服务是否在 6379 端口上开放。

### 4.1.3 安装配置 Elasticsearch

> iJEP 7 使用了 Elasticsearch 6 版本，不能下载安装 7 版本。

下载 [elasticsearch-6.8.20.zip](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.8.20.zip) 和  [elasticsearch-analysis-ik-6.8.20.zip](https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.8.20/elasticsearch-analysis-ik-6.8.20.zip)

解压 elasticsearch-6.8.20.zip 到 `C:\Users\Kevin\iJEP7\run-env\elasticsearch-6.8.20`，解压 elasticsearch-analysis-ik-6.8.20.zip 到 `C:\Users\Kevin\iJEP7\run-env\elasticsearch-6.8.20\plugins\ik` 目录，安装 ik 分词器。

双击 `C:\Users\Kevin\iJEP7\run-env\elasticsearch-6.8.20\elasticsearch-6.8.0\bin\` 下的 elasticsearch.bat 文件，启动 Elasticsearch。

为 Elasticsearch 添加 iJEP 7 中工作流引擎要用到的索引：

```bash
# 以下为 Linux 命令，可在 Git Bash 中使用 Linux 命令
curl -XPUT 'http://localhost:9200/proclog'
```

> 也可使用 Postman 向 http://localhost:9200/proclog 发送 PUT 操作。

### 4.1.4 安装配置 Consul

到 [官网](https://www.consul.io/) 下载 Windows 版本的 [Consul](https://releases.hashicorp.com/consul/1.10.4/consul_1.10.4_windows_amd64.zip)，解压 consul_1.10.4_windows_amd64.zip 到 `C:\Users\Kevin\iJEP7\run-env\consul_1.10.4_windows_amd64` 文件夹下。

创建 startup.bat 文件，以单节点方式启动，内容为：

```bash
consul.exe agent -dev -ui -client 0.0.0.0
```

打开浏览器，访问 [http://localhost:8500/](http://localhost:8500/) 地址，查看 Consul 的管理控制台。

### 4.1.5 安装配置 Nginx

在 Nginx 官网下载 [nginx-1.20.2.zip](http://nginx.org/download/nginx-1.20.2.zip)，解压到 `C:\Users\Kevin\iJEP7\run-env\nginx-1.20.2`。

后续，将打包好的前端应用部署（拷贝）到 `C:\Users\Kevin\iJEP7\run-env\nginx-1.20.2\html` 这个文件夹即可。

### 4.1.6 安装配置 MySQL

在 Windows 10 下安装 MySQL 5.7.35，需要提前安装微软的 VC++2013 依赖库 [vcredist_x64](http://download.microsoft.com/download/1/8/0/180fa2ce-506d-4032-aad1-9d7636f85179/vcredist_x64.exe)，可在微软官方网站下载并安装 vcredist_x64 依赖库。

到 MySQL 官网下载 [mysql-5.7.35-winx64.zip](https://downloads.mysql.com/archives/get/p/23/file/mysql-5.7.35-winx64.zip)，解压到 `C:\Users\Kevin\iJEP7\run-env\mysql-5.7.35-winx64`。

在 `C:\Users\Kevin\iJEP7\run-env\mysql-5.7.35-winx64` 下创建 my.ini 文件，内容如下：

```ini
[mysql]
# 设置mysql客户端默认字符集
default-character-set=utf8 
[mysqld]
# 设置3306端口
port = 3306 
# 设置mysql的安装目录
basedir=C:\\Users\\Kevin\\iJEP7\\run-env\\mysql-5.7.35-winx64
# 设置mysql数据库的数据的存放目录
datadir=C:\\Users\\Kevin\\iJEP7\\run-env\\mysql-5.7.35-winx64\\data
# 允许最大连接数
max_connections=200
# 设置mysql服务端默认字符集
character-set-server=utf8
# 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB 
# 注意这个参数，MySQL默认值导致在Windows 10 专业版上无法安装
innodb_flush_method=normal
```

以管理员身份运行命令行工具，并进入 `C:\Users\Kevin\iJEP7\run-env\mysql-5.7.35-winx64\bin` 目录，执行 MySQL 初始化命令 `mysqld --initialize-insecure`，在指定的目录中（数据目录配置 my.ini 配置文件中）初始化数据库，及生成 root 用户和无密码。

使用命令 `mysqld -install` 在Windows中安装MySQL系统服务；

使用命令 `net start mysql` 启动MySQL服务；

使用命令 `mysql -uroot -p` 登录MySQL数据库，提示输入密码时，直接回车；

使用命令 `set password = password('123456');` 修改root用户密码为123456。

### 4.1.7 导入数据

如果初始数据脚本很大，使用 MySQL 命令行导入时会很慢。

通过修改 MySQL 的磁盘写入策略以及数据安全性的参数，来提高数据导入速度，导入完成后，再修改回安全的参数配置。

```mysql
mysql -uroot -p

# 1.进入MySQL命令行 临时修改这两个参数
set global innodb_flush_log_at_trx_commit = 2;
set global sync_binlog = 2000;

# 2.执行SQL脚本导入
create database ijep7;
use ijep7;
source C:\\Users\\Kevin\\iJEP7\\run-env\\ijep_test.sql;

# 3.导入完成 再把参数改回来
set global innodb_flush_log_at_trx_commit = 1;
set global sync_binlog = 1;
```

#### 4.1.7.1 创建开发数据库

随开发试用环境分发的开发测试数据库是一个最小数据库，zip 后的大小为 52k。

请按照如下顺序创建并导入初始开发数据库：

1. 创建 ijep7 数据库；
2. 导入 ijep-db-init\flowable\flowable.mysql.all.create.sql，创建 flowable 表结构；
3. 导入 ijep-db-init\ijep7\ijep7.init.structure.create.sql，创建平台表结构；
4. 导入 ijep-db-init\ijep7\ijep7.init.data.sql，导入平台初始数据；
5. 导入 ijep-db-init\demo\ijep7.structure.dev.create.sql，创建开发演示数据表；
6. 导入 ijep-db-init\demo\ijep7.data.dev.sql，导入开发演示数据。

参考的 MySQL 命令行语句：

```bash
mysql -uroot -p

drop database ijep7;
create database ijep7;
use ijep7;

source D:/Kevin/iJEP7/ijep-run-env/ijep7-db/flowable/flowable.mysql.all.create.sql;
source D:/Kevin/iJEP7/ijep-run-env/ijep7-db/ijep7/ijep7.init.structure.create.sql;
source D:/Kevin/iJEP7/ijep-run-env/ijep7-db/ijep7/ijep7.init.data.sql;
source D:/Kevin/iJEP7/ijep-run-env/ijep7-db/demo/ijep7.structure.dev.create.sql;
source D:/Kevin/iJEP7/ijep-run-env/ijep7-db/demo/ijep7.data.dev.sql;
```

### 4.1.8 主机名映射

在开发阶段，为了方便系统部署，我们并没有使用 Apollo 配置中心，而是在工程中使用 application-dev.yml 文件对database、consul、redis、elasticsearch等外部资源使用主机名的方式进行了配置，所以你需要在开发机上根据实际机器 ip 地址信息对主机名进行配置。

以管理员身份运行命令行工具，输入 notepad 启动记事本，打开 C:\Windows\System32\drivers\etc\hosts 文件，添加如下配置：

```properties
127.0.0.1 server.consul
127.0.0.1 server.redis
127.0.0.1 server.elasticsearch
127.0.0.1 server.database
127.0.0.1 server.gateway
```

之后无需对应用进行修改，只需要修改目标机器的 hosts 文件即可。

### 4.1.9 部署应用

将 ijep-router-gateway.jar（API 网关）、ijep-service-sys.jar（系统服务）、ijep-service-bpm.jar（工作流引擎服务） 和 ijep-service-customer.jar（开发演示服务） 拷贝到 `C:\Users\Kevin\iJEP7\run-env\ijep-jars` 目录下，完成后端服务的部署；

将在 VS Code 中打包好的前端应用（如 .\app-ijep7-console\dist\）拷贝到 `C:\Users\Kevin\iJEP7\run-env\nginx-1.20.2\html` 目录下，完成前端应用的部署。

### 4.1.10 启动并测试

通过 Windows 的服务和/或对应支持软件的启动命令，依照以下建议顺序启动 iJEP 7 演示环境：

1. 启动 MySQL 数据库；
2. 启动 Redis；
3. 启动 Elasticsearch；
4. 启动 Consul；
5. 启动后端应用服务：
	打开命令行，依次启动网关、系统管理、工作流服务和开发测试（customer）服务
	5.1 java -jar -Dfile.encoding=utf-8 ijep-router-gateway.jar
	5.2 java -jar -Dfile.encoding=utf-8 ijep-service-sys.jar
	5.3 java -jar -Dfile.encoding=utf-8 ijep-service-bpm.jar
	5.4 java -jar -Dfile.encoding=utf-8 ijep-service-customer.jar
6. 启动部署了前端的 Nginx；
7. 启动 Chrome，访问 [http://localhost](http://localhost) 

为了方便演示，我们为 iJEP 7 配备了启动批处理文件 start-ijep7.bat 和 stop-nginx.bat：

**start-ijep7.bat：**

```powershell
@echo off
%1 mshta vbscript:CreateObject("Shell.Application").ShellExecute("cmd.exe","/c %~s0 ::","","runas",1)(window.close)&&exit
cd /d "%~dp0"

start cmd /c "title consul && .\consul_1.10.4_windows_amd64\consul agent -dev"
start cmd /c "title redis && .\Redis-x64-3.2.100\redis-server.exe"
start cmd /c "title elasticsearch && .\elasticsearch-6.8.20\bin\elasticsearch.bat"

set NGINX_PATH=%~d0
set NGINX_DIR=%~dp0nginx-1.20.2\
set NGINX_NAME=nginx.exe
set "PROCESS_LIST=tasklist /fi "imagename eq %NGINX_NAME%""
for /f "tokens=2" %%i in ('%PROCESS_LIST% /nh') do (
  taskkill /f /pid %%i
)
%NGINX_PATH%
cd %NGINX_DIR%
if exist "%NGINX_DIR%%NGINX_NAME%" (
  start "" %NGINX_NAME%
)
cd /d "%~dp0"

:: 延迟10秒
ping -n 10 127.0.0.1>nul

start cmd /c "title gateway && java -jar -Dfile.encoding=utf-8 .\ijep7-jars\ijep-router-gateway.jar"
start cmd /c "title sys && java -jar -Dfile.encoding=utf-8 .\ijep7-jars\ijep-service-sys.jar"

:: 延迟60秒后启动流程引擎，原因是流程引擎依赖sys服务
ping -n 60 127.0.0.1>nul

start cmd /c "title bpm && java -jar -Dfile.encoding=utf-8 .\ijep7-jars\ijep-service-bpm.jar"
start cmd /c "title customer && java -jar -Dfile.encoding=utf-8 .\ijep7-jars\ijep-service-customer.jar"
```

**stop-nginx.bat：**

```powershell
%1 mshta vbscript:CreateObject("Shell.Application").ShellExecute("cmd.exe","/c %~s0 ::","","runas",1)(window.close)&&exit

set NGINX_NAME=nginx.exe
tasklist /fi "imagename eq %NGINX_NAME%"
set "PROCESS_LIST=tasklist /fi "imagename eq %NGINX_NAME%""
for /f "tokens=2" %%i in ('%PROCESS_LIST% /nh') do (
  taskkill /f /pid %%i
)

pause
```

打开 Chrome 浏览器，输入地址：[http://localhost/](http://localhost/)，使用如下账户登录系统测试“请假流程”：

```
演示账号：wangwu 123456
部门经理审批：zhangsan 123456
行政审批：998 risk@2019
一级部门经理：lisi 123456
复核:  lengzw risk@2019
```

## 4.2 测试环境

测试环境通常采用 Linux 单机部署，以下我们以 CentOS 7 为例，详细介绍 iJEP7 测试环境的安装配置。

本测试环境假设部署 iJEP7 的 CentOS Linux 系统的 IP 地址为：192.168.137.200

> VirtualBox Linux 虚拟机设置 2 Core CPU + 16G 内存，在使用 host only 网卡配置的情况下，基本能满足测试要求。

### 4.2.1 安装配置 FastDFS

iJEP7 使用 FastDFS 作为附件存储服务，需要在 Linux 服务器上使用 root 用户安装运行 FastDFS。

FastDFS 使用源码形式发布，所以，Linux 服务器需要事先安装 GCC 编译环境。

#### 4.2.1.1 下载 FastDFS 软件

1. 下载 [libfastcommon 1.0.53](https://github.com/happyfish100/libfastcommon/archive/refs/tags/V1.0.53.tar.gz) 依赖包源码；
2. 下载 [FastDFS6.07](https://github.com/happyfish100/fastdfs/archive/refs/tags/V6.07.tar.gz) 源码；

#### 4.2.1.2 安装依赖包

libfastcommon 是 FastDFS 官方提供的，包含了 FastDFS 运行所需要的一些基础库。

使用 `tar -zxvf libfastcommon-1.0.53.tar.gz` 解压 libfastcommon-1.0.53 依赖包，然后使用如下命令安装：

```bash
cd libfastcommon-1.0.53
./make.sh
./make.sh install
```

然后，设置软链接：

```bash
ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so
ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so
```

#### 4.2.1.3 安装 FastDFS

使用 `tar -zxvf fastdfs-6.07.tar.gz` 解压 fastdfs-6.07 软件包，然后使用如下命令安装：

```bash
cd fastdfs-6.07
./make.sh
./make.sh install
```

安装完成后可以看到 /etc/fdfs 目录下生成了4个文件：

```bash
client.conf.sample
storage.conf.sample
storage_ids.conf.sample
tracker.conf.sample
```

#### 4.2.1.4 配置 tracker 节点

首先创建 tracker 的数据存储目录：

```bash
mkdir /data
mkdir /data/fastdfs
```

然后修改配置文件：

```bash
cd /etc/fdfs
mv tracker.conf.sample tracker.conf 
vim tracker.conf 
#修改其中的23行：  base_path = /data/fastdfs
#修改其中的312行： http.server_port = 6666
```

启动 tracker 服务：

```bash
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
```

启动成功后，FastDFS 会在 /data/fastdfs 目录下生成 data 和 logs 两个目录。

#### 4.2.1.5 配置 storage 节点

首先创建 storage 的数据存储目录：

```bash
mkdir /data
mkdir /data/fastdfs-storage
```

然后修改配置文件：

```bash
cd /etc/fdfs/
mv storage.conf.sample storage.conf
vim storage.conf
# 修改其中的49行：  base_path = /data/fastdfs-storage
# 修改其中的119行： store_path_count = 1
# 修改其中的122行： store_path0 = /data/fastdfs-storage
# 修改其中的145行： tracker_server = 192.168.137.200:22122
# 修改其中的352行： http.server_port = 8888
```

启动 storage 服务：

```bash
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
```

启动成功后，FastDFS 会在 /data/fastdfs-storage 目录下生成 data 和 logs 两个目录。

#### 4.2.1.5 配置 client （可选）

修改配置文件：

```bash
cd /etc/fdfs
mv client.conf.sample client.conf
vim client.conf
# 修改其中的11行：  base_path = /data/fastdfs
# 修改其中的13行：  tracker_server = 192.168.137.200:22122
```

测试上传 /etc/fdfs/storage_ids.conf.sample 文件到 FastDFS：

```bash
/usr/bin/fdfs_test /etc/fdfs/client.conf upload /etc/fdfs/storage_ids.conf.sample
```

查看上传到 FastDFS 文件系统中的文件存储情况：

```bash
cd /data/fastdfs-storage/data/00/00
ll
# total 16
# -rw-r--r-- 1 root root 620 Nov 28 16:24 wKiJyGGjPKGAWJmyAAACbDWRM1Q_big.sample
# -rw-r--r-- 1 root root  49 Nov 28 16:24 wKiJyGGjPKGAWJmyAAACbDWRM1Q_big.sample-m
# -rw-r--r-- 1 root root 620 Nov 28 16:24 wKiJyGGjPKGAWJmyAAACbDWRM1Q.sample
# -rw-r--r-- 1 root root  49 Nov 28 16:24 wKiJyGGjPKGAWJmyAAACbDWRM1Q.sample-m
```

### 4.2.2 安装配置 Redis

下载 [redis-6.2.6.tar.gz](https://download.redis.io/releases/redis-6.2.6.tar.gz)，解压到 /opt/redis-6.2.6，然后在 /opt/redis-6.2.6 目录中输入 make 编译 Redis，编译成功后，使用 `/opt/redis-6.2.6/src/redis-server` 启动 Redis 服务器。

### 4.2.3 安装配置 Elasticsearch

> iJEP 7 使用了 Elasticsearch 6 版本，不能下载安装 7 版本。

下载 [elasticsearch-6.8.20.tar.gz](https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.8.20.tar.gz) 和 [elasticsearch-analysis-ik-6.8.20.zip](https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v6.8.20/elasticsearch-analysis-ik-6.8.20.zip)，并解压到 `/opt/elasticsearch-6.8.20/` 目录。分词器解压位置为 `/opt/elasticsearch-6.8.20/plugins/ik/`。

ES 不能使用 root 用户启动，添加 es用户和组：

```bash
groupadd es
useradd es -g es
passwd es
cd /opt
chown -R es:es elasticsearch-6.8.20
```

使用 es 用户登录，启动 Elasticsearch

```bash
cd /opt/elasticsearch-6.8.20/bin/
./elasticsearch
```

为 Elasticsearch 添加 iJEP 7 中工作流引擎要用到的索引：

```bash
curl -XPUT 'http://localhost:9200/proclog'
```

### 4.2.4. 安装配置 RocketMQ

下载 [rocketmq-all-4.9.2-bin-release.zip](https://dlcdn.apache.org/rocketmq/4.9.2/rocketmq-all-4.9.2-bin-release.zip) 解压到服务器的 /opt/rocketmq-4.9.2 目录，进入 /opt/rocketmq-4.9.2/bin，依次启动 Name Server 和 Broker，完成 RocketMQ 的安装。

启动 Name Server：

```bash
nohup sh bin/mqnamesrv &
tail -f ~/logs/rocketmqlogs/namesrv.log
```

启动 Broker：

```bash
nohup sh bin/mqbroker -n localhost:9876 &
tail -f ~/logs/rocketmqlogs/broker.log 
```

### 4.2.5 安装配置 Consul

Consul 可以通过 CentOS 的 yum 来安装，在网络可用的情况下，请执行如下命令完成 Consul 的安装：

```bash
yum install -y yum-utils
yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
yum -y install consul
consul agent -dev
```

### 4.2.6 安装配置 Nginx

Nginx 可以通过 CentOS 的 yum 来安装，在网络可用的情况下，请执行如下命令完成 Nginx 的安装：

```bash
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
yum install -y nginx
systemctl start nginx.service
systemctl enable nginx.service
```

安装后的发布目录在 /usr/share/nginx/html，请将打包好的前端发布到这个目录。

### 4.2.7 安装配置 MySQL

下载 MySQL 5.7.32 需要用到的 5 个 rpm 包：

```bash
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-community-client-5.7.32-1.el7.x86_64.rpm
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-community-common-5.7.32-1.el7.x86_64.rpm
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-community-libs-5.7.32-1.el7.x86_64.rpm
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-community-libs-compat-5.7.32-1.el7.x86_64.rpm
wget https://cdn.mysql.com/archives/mysql-5.7/mysql-community-server-5.7.32-1.el7.x86_64.rpm
```

使用 yum 命令安装 MySQL 5.7.32：

```bash
yum install -y mysql-community-*-5.7.32-1.el7.x86_64.rpm
```

启动 MySQL 服务：

```bash
# 开启MySQL服务器
systemctl start mysqld.service
# 开启MySQL随机启动
systemctl enable mysqld.service
# 查看默认生成的初始密码
cat /var/log/mysqld.log | grep password
```

使用上面查询到的初始密码登录 MySQL 命令行控制台：

```bash
mysql -uroot -h127.0.0.1 -p
```

然后输入以下命令修改默认密码

```bash
# 设置密码等级
set global validate_password_policy=0;
set global validate_password_length=4;
# 修改默认密码，注意替换后面的密码
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```

查看表是否区分大小写：

```mysql
mysql> show variables like '%case%';
+------------------------------------+-------+
| Variable_name                      | Value |
+------------------------------------+-------+
| lower_case_file_system             | OFF   |
| lower_case_table_names             | 0     |
| validate_password_mixed_case_count | 1     |
+------------------------------------+-------+
3 rows in set (0.01 sec)
```

lower_case_table_names 配置为 0 则区分大小写，需要在 MySQL 的配置文件 /etc/my.cnf 中修改其值为 1：

```bash
vim /etc/my.cnf
# 在[mysqld]下加入一行：lower_case_table_names=1 //0代表区分大小写，1代表不区分大小写
# 保存后重启MySQL
systemctl restart mysqld.service 
```

进入 MySQL 命令行控制台，设置远端主机可以连接：

```mysql
use mysql;
select host, user, authentication_string, plugin from user;
create user 'root'@'%' IDENTIFIED by '123456';
grant all privileges on *.* to 'root'@'%' with grant option;
flush privileges;
```

退出 MySQL 控制台后重启 MySQL 服务：

```bash
systemctl restart mysqld.service
```

为 MySQL 设置一些参数：

```mysql
use mysql;
set sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

### 4.2.8 导入数据

由于初始数据脚本很大，使用 MySQL 命令行导入时会很慢。

通过修改 MySQL 的磁盘写入策略以及数据安全性的参数，来提高数据导入速度，导入完成后，再修改回安全的参数配置。

```mysql
mysql -uroot -p

# 1.进入MySQL命令行 临时修改这两个参数
set global innodb_flush_log_at_trx_commit = 2;
set global sync_binlog = 2000;

# 2.执行SQL脚本导入
create database ijep7;
use ijep7;
source /root/ijep_test.sql;

# 3.导入完成 再把参数改回来
set global innodb_flush_log_at_trx_commit = 1;
set global sync_binlog = 1;
```

### 4.2.9 部署应用

iJEP 7 是典型的前后端分离的应用，后端基于 Spring Boot/Cloud，前端基于 Vue，需要分别打包部署。

#### 4.2.9.1 后端微服务

使用 `mvn install` 编译安装 iJEP 7 基础源码包 ijep-parent（可选，如果不修改底层源码，则依赖 maven 私有仓库直接下载底层依赖包）。

依次修改后端微服务项目的配置（application-dev.yml），然后使用 `mvn package -DskipTests` 编译 ijep-router-gateway（网关）、ijep-sys-parent（系统服务）、ijep-bpm-parent（工作流引擎服务）和 ijep-service-customer（开发示例服务）。

> 主要是修改 application-dev.yml 本地配置文件，请使用主机名地址映射，以方便通过 hosts 指定特定的 IP 地址。

将打包好的 Spring Boot 应用 jar 包拷贝到服务器，如 /root/iJEP7/ijep-jars，完成应用的部署。

#### 4.2.9.2 前端控制台

进入前端项目 app-ijep7-console，执行 `npm run build:prod` 命令完成前端编译打包，打包后的文件在 app-ijep7-console\dist 位置。

```powershell
D:\Kevin\iJEP7\app-ijep7-console\dist>dir
 驱动器 D 中的卷是 data
 卷的序列号是 8C3B-9EC4

 D:\Kevin\iJEP7\app-ijep7-console\dist 的目录

2021/12/04  23:51    <DIR>          .
2021/12/04  23:51    <DIR>          ..
2021/12/04  23:51            67,646 favicon.ico
2021/12/04  23:51             5,016 index.html
2021/12/04  23:51    <DIR>          static
               2 个文件         72,662 字节
               3 个目录 135,610,347,520 可用字节

D:\Kevin\iJEP7\app-ijep7-console\dist>
```

将该 dist 目录下的所有文件拷贝到 Nginx 的 html 目录下，即完成前端控制台的部署。

### 4.2.10 主机名映射

在开发演示阶段，为了方便系统部署，我们并没有使用 Apollo 配置中心，而是在工程中使用 application-dev.yml 文件对数据库、redis、elasticsearch等外部资源使用主机名的方式进行了配置，所以你需要在开发演示机上根据实际机器 ip 地址信息对主机名进行配置。

在我的本机进行部署时的 hosts 配置如下：

```properties
127.0.0.1 server.gateway
127.0.0.1 server.database
127.0.0.1 server.redis
127.0.0.1 server.elasticsearch
127.0.0.1 server.rocketmq
127.0.0.1 server.consul
192.168.137.200 server.fastdfs
```

在部署到演示环境时，无需对应用进行修改，只需要修改目标机器的 hosts 文件即可。

### 4.2.11 启动并测试

Linux 下启动演示环境，将用到两个账号 root 和 es，在开发演示时为了排查问题，不建议将微服务启动为后台服务，以方便直接在终端查看运行日志。

#### 4.2.11.1 启动 MySQL

按照本文前面的步骤安装配置的 MySQL 是随操作系统一起启动的，如果没有配置随操作系统启动，则请使用 root 用户登录，执行如下命令脚本启动 MySQL 服务器：

```bash
systemctl restart mysqld.service 
```

#### 4.2.11.2 启动 FastDFS

单机的 FastDFS 由两个服务构成：tracker 和 storage：

##### 4.2.11.2.1 启动 tracker

使用 root 用户登录后执行如下命令启动 tracker：

```
/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf start
```

##### 4.2.11.2.2 启动 storage

使用 root 用户登录后执行如下命令启动 storage：

```
/usr/bin/fdfs_storaged /etc/fdfs/storage.conf start
```

#### 4.2.11.3 启动 Redis

使用 root 用户登录后执行如下命令启动 Redis：

```
/opt/redis-6.2.6/src/redis-server
```

#### 4.2.11.4 启动 Elasticsearch

使用 es 用户登录后执行如下命令启动 Elasticsearch：

```
/opt/elasticsearch-6.8.20/bin/elasticsearch
```

#### 4.2.11.5 启动 RocketMQ

单机的 RocketMQ 由两个服务构成：Name Server 和 Broker，需要依次启动。

##### 4.2.11.5.1 启动 Name Server

使用 root 用户登录后执行如下命令启动 RocketMQ Name Server：

```
nohup sh /opt/rocketmq-4.9.2/bin/mqnamesrv &
```

##### 4.2.11.5.2 启动 Broker

使用 root 用户登录后执行如下命令启动 RocketMQ Broker：

```
nohup sh /opt/rocketmq-4.9.2/bin/mqbroker -n localhost:9876 &
```

#### 4.2.11.6 启动 Consul

使用 root 用户登录后执行如下命令启动 Consul：

```
consul agent -dev
```

#### 4.2.11.7 启动 Nginx

按照本文前面的步骤安装配置的 Nginx 是随操作系统一起启动的，如果没有配置随操作系统启动，则请使用 root 用户登录，执行如下命令脚本启动 Nginx 服务器：

```
systemctl start nginx.service
```

#### 4.2.11.8 启动 iJEP 7

iJEP 7 是一个典型的 Spring Cloud 微服务应用，由网关服务、系统服务、工作流引擎服务和开发示例服务组成，请按照顺序启动这几个服务。

如果发现启动这几个服务特别慢，请使用 root 用户登录系统，查找 hostname，然后将其配置进 hosts 文件：

```
hostname #我的机器配置的 hostname 为 localhost.ijep7
vim /etc/hosts
# 将查询出来的 hostname 配置到第一行和第二行，如下面所示：
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 localhost.ijep7
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6 localhost.ijep7
```

##### 4.2.11.8.1 启动网关服务

使用 root 用户登录后执行如下命令启动**网关**服务：

```
java -jar -Dfile.encoding=utf-8 /root/iJEP7/ijep-jars/ijep-router-gateway.jar
```

##### 4.2.11.8.2 启动系统服务

使用 root 用户登录后执行如下命令启动**系统**服务：

```
java -jar -Dfile.encoding=utf-8 /root/iJEP7/ijep-jars/ijep-service-sys-7.0.0-SNAPSHOT.jar
```

##### 4.2.11.8.3 启动工作流引擎服务

使用 root 用户登录后执行如下命令启动**工作流引擎**服务：

```
java -jar -Dfile.encoding=utf-8 /root/iJEP7/ijep-jars/ijep-service-bpm-7.0.0-SNAPSHOT.jar
```

##### 4.2.11.8.4 启动开发示例服务

使用 root 用户登录后执行如下命令启动**开发示例**服务：

```
java -jar -Dfile.encoding=utf-8 /root/iJEP7/ijep-jars/ijep-service-customer-7.0.0-SNAPSHOT.jar
```

#### 4.2.11.9 测试

iJEP 7 WEB 前端在测试客户端（Windows 机器）上的浏览器（Chrome）中以 ajax 方式访问后台，而前端程序打包时使用了 `server.gateway` 的主机名来配置后端网关服务地址，所以需要注意测试客户端的 hosts 文件是否正确配置为 `192.168.137.200 server.gateway`，如果没有正确配置网关主机名地址映射，则会出现“<font color=red>**系统繁忙，请稍后再试！**</font>”的错误。

打开 Chrome 浏览器，输入地址：[http://192.168.137.200/](http://192.168.137.200/)，使用如下账户登录系统测试“请假流程”：

```
演示账号：wangwu 123456
部门经理审批：zhangsan 123456
行政审批：998 risk@2019
一级部门经理：lisi 123456
复核:  lengzw risk@2019
```

## 4.3 安装 Oceanbase

iJEP 7 在某西南省级农信联社需要使用 Oceanbase 分布式数据库，为了移植 iJEP 7 到 Oceanbase 数据库上，并做验证测试，所以提供本章节内容供给项目开发团队使用。

本章节使用 VirtualBox 安装 CenetOS 7 的虚拟机环境来安装配置最小的 OceanBase 服务器，供开发环境使用。

为了固定虚拟机内 IP 地址，采用了 Host-Only 网卡模式。

为了在安装配置过程中访问公网下载安装资源，为 Host-Only 网卡共享了宿主机的无线网卡。

> - 个人用户最低要求 2 核, 推荐 8 核及以上；
> - 个人用户户最低要求 8G, 推荐 64G 及以上；
> - 企业用户最低要求 16 核，推荐 32 核及以上；
> - 企业用户最低要求 64G，推荐 256G 及以上；
> - 如果您部署 OceanBase 集群，确保集群内的所有机器配置相同。

### 4.3.1 配置 host-only 访问公网

在宿主机（Windows 10）中，需要对 VirtualBox Host-Only 网卡设置 IP 地址和 DNS 地址：

> 一定要设置固定的 DNS 地址，因为笔记本接入公网经常采用的是 DHCP 方式。

![image-20211229002539211](images/image-20211229002539211.png)

在宿主机（Windows 10）中，能上网的那张网卡（笔记本上，通常是那张无线网卡）中设置将 Internet 连接共享给 VirtualBox Host-Only 网卡：

> 如果使用有线网卡，也同样设置即可。

![image-20211229002722030](images/image-20211229002722030.png)

检查**VirtualBox > 管理 > 主机网络管理器**的设置是否正确：

![image-20211230073901265](images/image-20211230073901265.png)

Linux 虚拟机中，检查网卡配置信息，网关和 DNS 都指向 192.168.137.1，宿主机中的 VirtualBox Host-Only 网卡里面的 DNS 设置会为虚拟机中的网卡做解析。

```properties
vi /etc/sysconfig/network-scripts/ifcfg-enp0s3
# 网卡配置信息如下：
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=no
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp0s3
UUID=e6ad7b0f-92cf-4d1d-9b53-ee5fa75e1ae5
DEVICE=enp0s3
ONBOOT=yes
IPADDR=192.168.137.200
PREFIX=24
GATEWAY=192.168.137.1
DNS1=192.168.137.1
```

重启虚拟机后，ping 百度网站，可以检查网络是否通畅：

```bash
ping www.baidu.com
64 bytes from 14.215.177.39 (14.215.177.39): icmp_seq=1 ttl=52 time=49.5 ms
64 bytes from 14.215.177.39 (14.215.177.39): icmp_seq=2 ttl=52 time=74.4 ms
64 bytes from 14.215.177.39 (14.215.177.39): icmp_seq=3 ttl=52 time=70.5 ms
64 bytes from 14.215.177.39 (14.215.177.39): icmp_seq=4 ttl=52 time=62.4 ms
64 bytes from 14.215.177.39 (14.215.177.39): icmp_seq=5 ttl=52 time=46.3 ms
```

### 4.3.2 安装 CentOS 虚拟机

在 VirtualBox 中新建 Linux 虚拟机，挂接 CentOS 安装镜像。

CentOS Linux 虚拟机（2C 16G）采用最小安装即可。 

![image-20211230071815524](images/image-20211230071815524.png)

为虚拟机设置固定 IP 地址为 192.168.137.200：

![image-20211230072154995](images/image-20211230072154995.png)

检查设置的 IP 地址信息：

> 确保网关和 DNS 都是设置为 VirtualBox Host-Only 网卡的地址：192.168.137.1

![image-20211230072328639](images/image-20211230072328639.png)

安装 Linux 的过程中创建 admin 用户。

安装完成重启后使用 root 用户登录，对 Linux 做相关设置。

#### 4.3.2.1 关闭 SELINUX

开发机器，为了方便，关闭 SELINUX。

编辑 /etc/selinux/config 文件，关闭 SELINUX：

```bash
vi /etc/selinux/config
# 修改的参数：
SELINUX=disabled
```

#### 4.3.2.2 关闭防火墙

开发机器，为了方便，关闭防火墙。

执行如下命令，关闭防火墙：

```bash
systemctl disable firewalld.service
```

#### 4.3.2.3 修改 open files 数

Linux 默认文件句柄数为 1024，远远不够用，修改成一个较大的值：

```bash
vi /etc/security/limits.conf
# 修改的参数：
* soft nofile 655360
* hard nofile 655360
```

#### 4.3.2.4 修改 fs.aio-max-nr 参数

在 Linux 中，fs.aio-max-nr 是个内核参数，指的是同时可以拥有的异步IO请求数目，Oceanbase 推荐的参数是 1024K，也就是 1048576：

```bash
vi /etc/sysctl.conf
# 修改的参数：
fs.aio-max-nr = 1048576
```

#### 4.3.2.5 关闭透明大页

对于 CentOS 操作系统，需要运行以下命令，手动关闭透明大页：

```bash
echo never > /sys/kernel/mm/transparent_hugepage/enabled
```

### 4.3.3 安装 Oceanbase

官方推荐使用 admin 用户登录安装 Oceanbase 数据库。

#### 4.3.3.1 安装部署工具

Oceanbase 官网 [quick start](https://open.oceanbase.com/quickStart) 是通过 [ob-deploy](https://mdn.alipayobjects.com/ob_portal/afts/file/A*vM1AQYaLHYIAAAAAAAAAAAAADmF2AQ?af_fileName=ob-deploy-1.1.2-1.el7.x86_64.rpm) 进行单机单节点安装部署的。

这样的安装方式可以快速的安装 Oceanbase 开发环境。

首选安装 [ob-deploy-1.1.2-1.el7.x86_64.rpm](https://mdn.alipayobjects.com/ob_portal/afts/file/A*vM1AQYaLHYIAAAAAAAAAAAAADmF2AQ?af_fileName=ob-deploy-1.1.2-1.el7.x86_64.rpm)，在 admin 用户登录时要 su 到 root 用户安装：

```bash
[admin@server ~]$ su
Password:
[root@server admin]# rpm -ivh ob-deploy-1.1.2-1.el7.x86_64.rpm
warning: ob-deploy-1.1.2-1.el7.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID e9b4a7aa: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:ob-deploy-1.1.2-1.el7            ################################# [100%]
Installation of obd finished successfully
Please source /etc/profile.d/obd.sh to enable it
```

安装完成后，按照提示执行 `source /etc/profile.d/obd.sh` 命令，最后退出 su，回到 admin 用户。

#### 4.3.3.2 安装 Oceanbase

创建并编辑 config.yaml 配置文件，内容如下：

> 一定要注意修改 memory_limit、system_memory 和 datafile_size 参数，否则小小的虚拟机没有那么多内存，根本启动不起来，[官方文档](https://www.oceanbase.com/docs/oceanbase-database/oceanbase-database/V3.2.1/what-is-oceanbase)没有提到这一点。

```yaml
user:
  username: admin
  password: gy@Zhang
oceanbase-ce:
  servers:
  - 192.168.137.200
  global:
    home_path: /home/admin/observer/
    devname: enp0s3
    appname: obcluster
  192.168.137.200:
    syslog_level: INFO
    enable_syslog_recycle: true
    enable_syslog_wf: true
    max_syslog_file_count: 4
    memory_limit: 8G
    system_memory: 4G
    cpu_count: 4
    datafile_size: 4G
    clog_disk_utilization_threshold: 94
    clog_disk_usage_limit_percentage: 98
auto_create_tenant: true
```

> 上面 devname: enp0s3 是网卡的名称。

连接公网，执行 `obd cluster autodeploy ijep7 -c config.yaml -A` 命令创建集群并启动：

```bash
[admin@server ~]$ obd cluster autodeploy ijep7 -c config.yaml -A
Update OceanBase-community-stable-el7 ok
Update OceanBase-development-kit-el7 ok
Download oceanbase-ce-3.1.1-4.el7.x86_64.rpm (46.21 M):   0% [] ETA:  --:--:--
...
Download oceanbase-ce-3.1.1-4.el7.x86_64.rpm (46.21 M): 100% [] Time: 0:04:04 197.98 kB/s
Package oceanbase-ce-3.1.1 is available.
install oceanbase-ce-3.1.1 for local ok
Cluster param config check ok
Open ssh connection ok
Generate observer configuration ok
oceanbase-ce-3.1.1 already installed.
+-----------------------------------------------------------------------------+
|                                   Packages                                  |
+--------------+---------+---------+------------------------------------------+
| Repository   | Version | Release | Md5                                      |
+--------------+---------+---------+------------------------------------------+
| oceanbase-ce | 3.1.1   | 4.el7   | f19f8bfb67723712175fb0dfd60579196b3168f1 |
+--------------+---------+---------+------------------------------------------+
Repository integrity check ok
Parameter check ok
Open ssh connection ok
Remote oceanbase-ce-3.1.1-f19f8bfb67723712175fb0dfd60579196b3168f1 repository i             nstall ok
Remote oceanbase-ce-3.1.1-f19f8bfb67723712175fb0dfd60579196b3168f1 repository l             ib check !!
[WARN] 192.168.137.200 oceanbase-ce-3.1.1-f19f8bfb67723712175fb0dfd60579196b316             8f1 require: libmariadb.so.3

Try to get lib-repository
Download oceanbase-ce-libs-3.1.1-4.el7.x86_64.rpm (155.15 K):   0% [] ETA:  --:       ...
Download oceanbase-ce-libs-3.1.1-4.el7.x86_64.rpm (155.15 K): 100% [] Time: 0:00:00 261.89 kB/s
Package oceanbase-ce-libs-3.1.1 is available.
install oceanbase-ce-libs-3.1.1 for local ok
Use oceanbase-ce-libs-3.1.1-58384f7ab4ee736e9d530f4bdd63c20ced0e7aba for oceanb             ase-ce-3.1.1-f19f8bfb67723712175fb0dfd60579196b3168f1
Remote oceanbase-ce-libs-3.1.1-58384f7ab4ee736e9d530f4bdd63c20ced0e7aba reposit             ory install ok
Remote oceanbase-ce-3.1.1-f19f8bfb67723712175fb0dfd60579196b3168f1 repository l             ib check ok
Cluster status check ok
Initializes observer work home ok
ijep7 deployed
Get local repositories and plugins ok
Open ssh connection ok
Cluster param config check ok
Check before start observer ok
[WARN] (192.168.137.200) clog and data use the same disk (/home)

Start observer ok
observer program health check ok
Connect to observer ok
Initialize cluster
Cluster bootstrap ok
Create tenant test ok
Wait for observer init ok
+---------------------------------------------------+
|                      observer                     |
+-----------------+---------+------+-------+--------+
| ip              | version | port | zone  | status |
+-----------------+---------+------+-------+--------+
| 192.168.137.200 | 3.1.1   | 2881 | zone1 | active |
+-----------------+---------+------+-------+--------+

ijep7 running
```

执行 `obd cluster display ijep7` 命令查看集群启动状态：

```bash
[admin@server ~]$ obd cluster display ijep7
Get local repositories and plugins ok
Open ssh connection ok
Cluster status check ok
Connect to observer ok
Wait for observer init ok
+---------------------------------------------------+
|                      observer                     |
+-----------------+---------+------+-------+--------+
| ip              | version | port | zone  | status |
+-----------------+---------+------+-------+--------+
| 192.168.137.200 | 3.1.1   | 2881 | zone1 | active |
+-----------------+---------+------+-------+--------+
```

使用如下命令启动停止集群：

```bash
obd cluster start ijep7
obd cluster stop ijep7
```

![image-20211229150059833](images/image-20211229150059833.png)

#### 4.3.3.3 安装客户端

到官网下载 [libobclient-2.0.0-2.el7.x86_64.rpm](https://mdn.alipayobjects.com/ob_portal/afts/file/A*UK3jRq1oVVYAAAAAAAAAAAAADmF2AQ?af_fileName=libobclient-2.0.0-2.el7.x86_64.rpm) 和 [obclient-2.0.0-2.el7.x86_64.rpm](https://mdn.alipayobjects.com/ob_portal/afts/file/A*fO_ZQbLQUZQAAAAAAAAAAAAADmF2AQ?af_fileName=obclient-2.0.0-2.el7.x86_64.rpm) 并 su 切换到 root 用户安装：

```bash
[admin@server ~]$ su
Password:
[root@server admin]# rpm -ivh libobclient-2.0.0-2.el7.x86_64.rpm
warning: libobclient-2.0.0-2.el7.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID e9b4a7aa: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:libobclient-2.0.0-2.el7          ################################# [100%]
[root@server admin]# rpm -ivh obclient-2.0.0-2.el7.x86_64.rpm
warning: obclient-2.0.0-2.el7.x86_64.rpm: Header V4 RSA/SHA1 Signature, key ID e9b4a7aa: NOKEY
Preparing...                          ################################# [100%]
Updating / installing...
   1:obclient-2.0.0-2.el7             ################################# [100%]
[root@server admin]# exit
exit
[admin@server ~]$
```

#### 4.3.3.4 操作数据库

使用 obclient 连接到数据库：

```bash
[admin@server ~]$ obclient -h192.168.137.200 -P2881 -uroot
Welcome to the OceanBase.  Commands end with ; or \g.
Your MySQL connection id is 3221488330
Server version: 5.7.25 OceanBase 3.1.1 (r4-8c615943cbd25a6f7b8bdfd8677a13a21709a05e) (Built Oct 21 2021 10:33:14)

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MySQL [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| oceanbase          |
| information_schema |
| mysql              |
| SYS                |
| LBACSYS            |
| ORAAUDITOR         |
| test               |
+--------------------+
7 rows in set (0.003 sec)

MySQL [(none)]> use oceanbase;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MySQL [oceanbase]>
```

修改 root 用户的密码：

```bash
MySQL [oceanbase]> alter user root identified by '123456';
Query OK, 0 rows affected (0.066 sec)

MySQL [oceanbase]>
```

在宿主机（Windows 10）上，使用 MySQL 客户端，直接连接到 OceanBase，使用 MySQL 命令创建 ijep7 数据库，导入数据。

```bash
C:\Program Files\mysql-5.7.29-winx64\bin>mysql -h192.168.137.200 -P2881 -uroot -p
Enter password: ******
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 3221488395
Server version: 5.7.25 OceanBase 3.1.1 (r4-8c615943cbd25a6f7b8bdfd8677a13a21709a05e) (Built Oct 21 2021 10:33:14)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> create database ijep7;
Query OK, 1 row affected (0.05 sec)

mysql> use ijep7;
Database changed
mysql> source d:/flowable.mysql.all.create.sql;
mysql> source d:/ijep7.mysql.init.structure.sql;
mysql> source d:/ijep7.mysql.init.data.sql;
mysql> source d:/ijep7.mysql.dev.structure.sql;
mysql> source d:/ijep7.mysql.dev.data.sql;
mysql> exit
Bye
```

使用 Navicat 以 MySQL 方式建立连接，进行数据管理：

![image-20211230232804192](images/image-20211230232804192.png)
