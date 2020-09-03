## Docker的安装配置与应用

> # <span id="home">目录</span>
> 
> * [1. 安装前准备（*Linux*）](#1)
>   * [1.1 升级Bash](#1-1)
>   * [1.2 更新系统](#1-2)
> 
> * [2. 安装*Docker*](#2)
>   * [2.1 安装Docker](#2-1)
> 
> * [3. 配置*Docker*](#3)
>   * [3.1 配置Docker](#3-1)
>   * [3.2 配置Swarm](#3-1)
> 
> * [4. 安装*Portainer*](#4)
>   * [4.1 安装web服务器（Nginx）（不需要）](#4-1)
>   * [4.2 安装web服务器（Apache）（不需要）](#4-2)
>   * [4.3 安装Portainer（使用阿里云镜像加速器）](#4-3)
> 
> * [5. 配置*Portainer*](#5)
>   * [5.1 配置Portainer](#5-1)
> 
> * [6. 使用*Portainer*快速部署](#6)
>   * [6.1 MariaDB](#6-1)
>   * [6.2 MySQL](#6-2)
>   * [6.3 Redis](#6-3)
>   * [6.4 MongoDB](#6-4)
>   * [6.5 WordPress](#6-5)
>   * [6.6 Microsoft SQLServer Linux(2017)](#6-6)
>   * [6.7 Microsoft SQLServer 2017](#6-7)
>   * [6.8 Microsoft SQLServer 2019](#6-8)
>   * [6.9 gitlab](#6-9)
>   * [6.10 jenkins](#6-10)
>   * [6.11 Caddy](#6-11)
>   * [6.12 CentOS7](#6-12)
>   * [6.13 OpenProject](#6-13)
>   * [6.14 禅道开源版](#6-14)
>   * [6.15 Speedtest（局域网内网速测试）](#6-15)
>   * [6.16 Nginx](#6-16)

<h1 id="1">1. 安装前准备（*Linux*）</h1>
本环境中使用**CentOS7**安装与配置Docker等软件。  

<h2 id="1-1">1.1 升级Bash</h2>

##### 1.1.1 查看当前Bash版本
    bash --version

##### 1.1.2 升级Bash  
> [Bash下载地址](http://ftp.gnu.org/gnu/bash/ "Bash下载地址")  

此处以更新到Bash4.4为例

````
cd
mkdir bash && cd bash
wget http://ftp.gnu.org/gnu/bash/bash-4.4.tar.gz
tar -zxvf bash-4.4.tar.gz
cd bash-4.4.tar.gz
./configure && make && make install
````

安装完成后查看当前Bash版本  

    bash --version

[返回目录](#home)

<h2 id="1-2">1.2 更新系统</h2>
    yum -y update

##### 1.2.1 删除旧版内核  
````
#查看当前内核版本
uname -r
#查看已安装的内核
rpm -q kernel
#删除旧版内核
yum remove kernel-3.10.0-1062.el7.x86_64
````

[返回目录](#home)

<h1 id="2">2. 安装*Docker*</h1>
<h2 id="2-1">2.1 安装Docker</h2>

##### 2.1.1 使用阿里云镜像源安装
[参考链接](https://developer.aliyun.com/mirror/docker-ce "Docker CE 镜像")

````
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
# Step 2: 添加软件源信息
sudo yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# Step 3: 更新并安装Docker-CE
sudo yum makecache fast
sudo yum -y install docker-ce
# Step 4: 开启Docker服务
sudo service docker start
# Step 5: 设置Docker开机启动
systemctl enable docker.service

# 注意：
# 官方软件源默认启用了最新的软件，您可以通过编辑软件源的方式获取各个版本的软件包。例如官方并没有将测试版本的软件源置为可用，您可以通过以下方式开启。同理可以开启各种测试版本等。
# vim /etc/yum.repos.d/docker-ee.repo
#   将[docker-ce-test]下方的enabled=0修改为enabled=1
#
# 安装指定版本的Docker-CE:
# Step 1: 查找Docker-CE的版本:
# yum list docker-ce.x86_64 --showduplicates | sort -r
#   Loading mirror speeds from cached hostfile
#   Loaded plugins: branch, fastestmirror, langpacks
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            docker-ce-stable
#   docker-ce.x86_64            17.03.1.ce-1.el7.centos            @docker-ce-stable
#   docker-ce.x86_64            17.03.0.ce-1.el7.centos            docker-ce-stable
#   Available Packages
# Step2: 安装指定版本的Docker-CE: (VERSION例如上面的17.03.0.ce.1-1.el7.centos)
# sudo yum -y install docker-ce-[VERSION]
````

##### 2.1.2 配置 Docker 镜像站
###### 2.1.2.1 使用中国科技大学的docker镜像源
````
#在/etc/docker/daemon.json文件中添加下面参数
#增加配置文件/etc/docker/daemon.json
{
"registry-mirrors" : ["https://docker.mirrors.ustc.edu.cn"]
}
#重启Docker生效
sudo systemctl restart docker
````

###### 2.1.2.2 使用阿里云镜像加速器
````
#登录阿里云并获取加速器地址
https://cr.console.aliyun.com/cn-shanghai/instances/mirrors
#配置镜像加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": ["https://你的加速器地址.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
````

[返回目录](#home)

<h1 id="3">3. 配置*Docker*</h1>
<h2 id="3-1">3.1 配置Docker</h2>

> [docker docs](https://docs.docker.com/reference/ "docker docs")

[返回目录](#home)

<h1 id="4">4. 安装*Portainer*</h1>
<h2 id="4-1">4.1 安装web服务器（Nginx）（不需要）</h2>

````
#安装必备组件
sudo yum install yum-utils

#配置repo源
vim /etc/yum.repos.d/nginx.repo

#粘贴以下内容
[nginx-stable]
name=nginx stable repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=1
enabled=1
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

[nginx-mainline]
name=nginx mainline repo
baseurl=http://nginx.org/packages/mainline/centos/$releasever/$basearch/
gpgcheck=1
enabled=0
gpgkey=https://nginx.org/keys/nginx_signing.key
module_hotfixes=true

#安装nginx
sudo yum -y install nginx

#启动nginx并设置开机启动
systemctl enable nginx.service
systemctl start nginx.service

#配置防火墙开启80端口
firewall-cmd --zone=public --add-port=80/tcp --permanent
systemctl restart firewalld.service

#nginx相关配置
#网站文件存放默认位置（Welcome to nginx 页面）
/usr/share/nginx/html

#网站默认站点配置
/etc/nginx/conf.d/default.conf

#自定义nginx站点配置文件存放目录
/etc/nginx/conf.d/

#nginx 全局配置文件
/etc/nginx/nginx.conf

#测试 nginx 配置
nginx -t

#启动 nginx
service nginx start

#关闭 nginx
service nginx stop

#重启 nginx
service nginx reload
````

[返回目录](#home)

<h2 id="4-2">4.2 安装web服务器（Apache）（不需要）</h2>

[返回目录](#home)

<h2 id="4-3">4.3 安装Portainer（使用阿里云镜像加速器）</h2>

````
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

http://ip:9000
用户名密码admin 11111111
````

[返回目录](#home)

<h1 id="5">5. 配置*Portainer*</h1>
<h2 id="5-1">5.1 配置Portainer</h2>

##### 5.1.1 Images
左侧列表选择Images
DockerHub镜像地址  
https://hub.docker.com/search?type=image  
根据Tags中的版本，下载对应镜像，如输入：mariadb:latest

[返回目录](#home)

<h1 id="6">6. 使用*Portainer*快速部署</h1>
<h2 id="6-1">6.1 MariaDB</h2>

拉取镜像（mariadb:latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：mariadb:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：3306  

Advanced container settings  
Env  
Environment variables->add environment variable
> name：MYSQL_ROOT_PASSWORD，value：默认root密码

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-2">6.2 MySQL</h2>

拉取镜像（mysql:5.7）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：mysql:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：3306  

Advanced container settings  
Env  
Environment variables->add environment variable
> name：MYSQL_ROOT_PASSWORD，value：默认root密码

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-3">6.3 Redis</h2>

拉取镜像（redis:latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：redis:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：6379  

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-4">6.4 MongoDB</h2>

拉取镜像（mongo:lates）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：mongo:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：27017  

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-5">6.5 WordPress</h2>

以Stack形式部署
首先拉取mysql（mysql:5.7）和wordpress（wordpress:latest）镜像

````yml
version: "2.4"
services:

  mysql:
    image: mysql:5.7
    ports:
      - "93:3306"
    volumes:
      - finallywordpress_data:/var/lib/mysql
    restart: always
    #container_name: mysql #指定容器名
    environment:
      MYSQL_ROOT_PASSWORD: mysqlrootpwd
      MYSQL_DATABASE: db_wordpress
      MYSQL_USER: user_wordpress
      MYSQL_PASSWORD: pwd_wordpress

  wordpress:
    image: wordpress:latest
    #设置依赖（不会等mysql启动完成后再启动wordpress）
    #depends_on:
    #  - mysql
    #链接到该容器
    links:
      - mysql
      #- mysql:wordpressmysql #设置别名
    ports:
      - "92:80"
    volumes:
      #将容器内容的目录挂在到外部（：左边同volumes中一样，右边容器中路径）
      - wordpress_data:/var/www/html
    restart: always
    #container_name: wordpress #指定容器名
    environment:
      #WORDPRESS_DB_HOST: wordpressmysql:3306#使用别名，端口需为内部端口号
      WORDPRESS_DB_HOST: mysql:3306
      WORDPRESS_DB_USER: user_wordpress
      WORDPRESS_DB_PASSWORD: pwd_wordpress
      WORDPRESS_DB_NAME: db_wordpress

volumes:
  finallywordpress_data:
  wordpress_data:
````

[返回目录](#home)

<h2 id="6-6">6.6 Microsoft SQLServer Linux(2017)</h2>

https://hub.docker.com/r/microsoft/mssql-server-linux  
https://docs.microsoft.com/zh-cn/sql/linux/quickstart-install-connect-docker?view=sql-server-ver15&pivots=cs1-bash  
拉取镜像（microsoft/mssql-server-linux:latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：microsoft/mssql-server-linux:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：1433  

Advanced container settings  
Env  
Environment variables->add environment variable  
#同意协议，必填
> name：ACCEPT_EULA，value：Y
> 
#密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、十进制数字和符号，必填
> name：SA_PASSWORD，value：<YourStrong@Passw0rd\>

点击Deploy the container（确保firewall处于运行状态）

注意：使用Navicat连接mssql时，如有指定端口，主机需填成：ip,端口

[返回目录](#home)

<h2 id="6-7">6.7 Microsoft SQLServer 2017</h2>

https://hub.docker.com/_/microsoft-mssql-server  
拉取镜像（mcr.microsoft.com/mssql/server:2017-latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：mcr.microsoft.com（在Registries中新增一个，URL：mcr.microsoft.com）  
> Image：mssql/server:2017-latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：1433  

Advanced container settings  
Env  
Environment variables->add environment variable  
#同意协议，必填
> name：ACCEPT_EULA，value：Y
> 
#密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、十进制数字和符号，必填
> name：SA_PASSWORD，value：<YourStrong@Passw0rd\>

点击Deploy the container（确保firewall处于运行状态）

注意：使用Navicat连接mssql时，如有指定端口，主机需填成：ip,端口

[返回目录](#home)

<h2 id="6-8">6.8 Microsoft SQLServer 2019</h2>

https://hub.docker.com/_/microsoft-mssql-server  
拉取镜像（mcr.microsoft.com/mssql/server:2019-latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：mcr.microsoft.com（在Registries中新增一个，URL：mcr.microsoft.com）  
> Image：mssql/server:2019-latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：1433  

Advanced container settings  
Env  
Environment variables->add environment variable  
#同意协议，必填
> name：ACCEPT_EULA，value：Y
> 
#密码的长度必须至少为 8 个字符，并且必须包含以下四种字符中的三种：大写字母、小写字母、十进制数字和符号，必填
> name：SA_PASSWORD，value：<YourStrong@Passw0rd\>

点击Deploy the container（确保firewall处于运行状态）

注意：使用Navicat连接mssql时，如有指定端口，主机需填成：ip,端口

[返回目录](#home)

<h2 id="6-9">6.9 gitlab</h2>

https://hub.docker.com/r/gitlab/gitlab-ce  
拉取镜像（gitlab/gitlab-ce:latest）

[返回目录](#home)

<h2 id="6-10">6.10 jenkins</h2>

https://hub.docker.com/r/jenkins/jenkins  
> 长期支持版本 (LTS)：jenkins/jenkins:lts  
> LTS (长期支持) 版本每12周从常规版本流中选择，作为该时间段的稳定版本。  
> 
> 每周更新版：jenkins/jenkins  
> 每周都会发布一个新版本，为用户和插件开发人员提供错误修复和功能。

新建数据卷  
Volumes->Add volume  

> Name：数据卷名  

点击Create the volume  

拉取镜像（jenkins/jenkins:lts）  
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：jenkins/jenkins:lts  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：8080  
> host：外部访问端口，container：50000  

Advanced container settings  
Volumes  
Volume mapping->+map additional volume  
> container：/var/jenkins_home，volume：选择之前新建的Volume #需提前新建

点击Deploy the container（确保firewall处于运行状态）  

浏览器打开ip:端口（容器内8080对应的外部端口）  

FAQ：  
> 问：  
> 打开Jenkins一直显示“Please wait while Jenkins is getting ready to work”的解决办法  
> 答：  
> 网络问题导致  
> 打开Volumes，复制之前新建Jenkins volume对应的Mount point，此处以/var/lib/docker/volumes/jenkins_data/_data为例  
> ssh进宿主机  
> ```
> vim /var/lib/docker/volumes/jenkins_data/_data/hudson.model.UpdateCenter.xml  
> ```
> 将第五行中的地址替换成：  
> ````
> https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json  
> ````
> 保存、退出、在Containers中重启容器  
> 浏览器重新打开Jenkins显示登录页面  
> 密码可以通过Containers->容器对应Quick actions下Logs中查看，在Please use the following password to proceed to installation:下面一行；或通过Containers->容器对应Quick actions下Exec Console->点击Connect，输入cat /var/jenkins_home/secrets/initialAdminPassword 查看密码  

> 问：  
> Jenkins下载插件过慢  
> 答：  
> 打开Volumes，复制之前新建Jenkins volume对应的Mount point，此处以/var/lib/docker/volumes/jenkins_data/_data为例  
> ssh进宿主机  
> ```
> cd /var/lib/docker/volumes/jenkins_data/_data/updates/  
> 
> sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json && sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
> ```
> 保存、退出、在Containers中重启容器  

[返回目录](#home)

<h2 id="6-11">6.11 Caddy</h2>

https://hub.docker.com/_/caddy  

新建数据卷  
Volumes->Add volume  

> Name：数据卷名  

点击Create the volume，需要建两个，一个data，一个config  

拉取镜像（caddy:latest）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：caddy:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：80  

Advanced container settings  
Volumes  
Volume mapping->+map additional volume  
> container：/usr/share/caddy，volume：选择之前新建的Volume #需提前新建  
> container：/config，volume：选择之前新建的Volume #需提前新建  

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-12">6.12 CentOS7</h2>

https://hub.docker.com/_/centos  
拉取镜像（centos:centos7）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：centos:centos7  

Advanced container settings  
Command & logging  
> Command：/usr/sbin/init  

点击Deploy the container（确保firewall处于运行状态）

注意：使用Navicat连接mssql时，如有指定端口，主机需填成：ip,端口

[返回目录](#home)

<h2 id="6-13">6.13 OpenProject</h2>

https://hub.docker.com/r/openproject/community  
拉取镜像（openproject/community:10）
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：openproject/community:10  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：80  

Advanced container settings  
Volumes  
Volume mapping->+map additional volume  
> container：/var/lib/openproject/pgdata，volume：选择之前新建的Volume #需提前新建  
> container：/var/lib/openproject/static，volume：选择之前新建的Volume #需提前新建  
> 
Env  
Environment variables->add environment variable  
> name：SECRET_KEY_BASE，value：secret  

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-14">6.14 禅道开源版</h2>

> [官网文档](https://www.zentao.net/book/zentaopmshelp/405.html "官网文档")  

https://hub.docker.com/r/easysoft/zentao  
拉取镜像（easysoft/zentao:latest）默认latest为禅道开源最新版本
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：easysoft/zentao:latest  

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：80  

Advanced container settings  
Volumes  
Volume mapping->+map additional volume  
> container：/app/zentaopms，volume：选择之前新建的Volume #需提前新建  
> container：/app/mysqldata，volume：选择之前新建的Volume #需提前新建  
> 
Env  
Environment variables->add environment variable  
> name：MYSQL_ROOT_PASSWORD，value：数据库密码  

点击Deploy the container（确保firewall处于运行状态）

[返回目录](#home)

<h2 id="6-15">6.15 Speedtest（局域网内网速测试）</h2>

> [官网GitHub](https://github.com/librespeed/speedtest "官网GitHub")  

https://hub.docker.com/r/adolfintel/speedtest  
拉取镜像（adolfintel/speedtest:latest）  
Containers->Add container  

Name：容器名  

Image configuration  
> Registry：DockerHub  
> Image：adolfintel/speedtest:latest

Network ports configuration  
Manual network port publishing->publish a new network port  
> host：外部访问端口，container：80  

点击Deploy the container（确保firewall处于运行状态）

注：若在虚拟机中部署的docker，且网络使用NAT，需要在虚拟机网络中配置端口转发

[返回目录](#home)

<h2 id="6-16">6.16 Nginx</h2>

<h3 id="6-16-1">6.16.1 拉取镜像</h3>

nginx:stable

<h3 id="6-16-2">6.16.2 部署前准备</h3>

新建数据卷

Volumes->Add volume  

> Name：nginx_file（数据卷名），存放nginx配置文件（nginx安装路径/etc/nginx）
> Name：nginx_data（数据卷名），存放网站路径（nginx默认网站路径/usr/share/nginx）

点击Create the volume

<h3 id="6-16-3">6.16.3 开始部署</h3>

以Stack形式部署

````yml
web:
  image: nginx:stable             #拉取的镜像Tag，若本地没有镜像，则拉取
  #container_name: nginx          #指定容器名
  volumes:
   - nginx_file:/etc/nginx        #:左侧为6.16.2中新建的数据卷名称，右侧为容器中对应路径
   - nginx_data:/usr/share/nginx  #:左侧为6.16.2中新建的数据卷名称，右侧为容器中对应路径
  ports:
   - "107:80"                     #:左侧为外部访问端口，右侧为容器内端口
````

[返回目录](#home)