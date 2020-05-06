## Docker的安装与配置
> #### 安装前准备（*Linux*）
> #### 安装*Docker*
> #### 配置*Docker*
> #### 安装*Portainer*
> #### 配置*Portainer*

## 正文
### 1. 安装前准备（*Linux*）
本环境中使用**CentOS7**安装与配置Docker等软件。  

#### 1.1 升级Bash  

##### 1.1.1 查看当前Bash版本
    bash --version

##### 1.1.2 升级Bash  
> [Bash下载地址](http://ftp.gnu.org/gnu/bash/ "Bash下载地址")  

此处以更新到Bash4.4为例

    cd
    mkdir bash && cd bash
    wget http://ftp.gnu.org/gnu/bash/bash-4.4.tar.gz
    tar -zxvf bash-4.4.tar.gz
    cd bash-4.4.tar.gz
    ./configure && make && make install

安装完成后查看当前Bash版本  

    bash --version

#### 1.2 更新系统  
    yum -y update

##### 1.2.1 删除旧版内核  
    #查看当前内核版本
    uname -r
    #查看已安装的内核
    rpm -q kernel
    #删除旧版内核
    yum remove kernel-3.10.0-1062.el7.x86_64

### 2. 安装*Docker*
#### 2.1 安装Docker

##### 2.2.1 使用阿里云镜像源安装
[参考链接](https://developer.aliyun.com/mirror/docker-ce "Docker CE 镜像")

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

##### 2.2.1 配置 Docker 镜像站
###### 2.2.1.1 使用中国科技大学的docker镜像源
    #在/etc/docker/daemon.json文件中添加下面参数
    #增加配置文件/etc/docker/daemon.json
    {
    "registry-mirrors" : ["https://docker.mirrors.ustc.edu.cn"]
    }
    #重启Docker生效
    sudo systemctl restart docker

###### 2.2.1.2 使用阿里云镜像加速器
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


### 3. 配置*Docker*
#### 3.1 配置Docker
> [docker docs](https://docs.docker.com/reference/ "docker docs")


### 4. 安装*Portainer*
#### 4.1 安装web服务器（nginx）
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

#### 4.2 安装web服务器（Apache）



#### 4.3 安装Portainer（使用阿里云镜像加速器）
    docker volume create portainer_data
    docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer

    http://ip:9000
    用户名密码admin 11111111

### 5. 配置*Portainer*
#### 5.1 配置Portainer
##### 5.1.2 Images
左侧列表选择Images
DockerHub镜像地址  
https://hub.docker.com/search?type=image  
根据Tags中的版本，下载对应镜像，如输入：mariadb:latest


### 6. 使用*Portainer*快速部署
#### 6.1 MariaDB
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