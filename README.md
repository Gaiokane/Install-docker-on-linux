## Docker的安装与配置
> #### 安装前准备（*Linux*）
> #### 安装*Docker*
> #### 配置*Docker*
> #### 安装*Portainer*
> #### 配置*Portainer*

## 正文
### 1. 安装前准备（*Linux*）
本环境中使用**CentOS7**安装与配置Docker等软件。  

#### 1.1升级Bash  

##### 1.1.1查看当前Bash版本
    bash --version

##### 1.1.2升级Bash  
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






### 2. 安装*Docker*
**Docker**