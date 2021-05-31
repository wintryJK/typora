# Linux学习

#### 一、各种运行问题

1. sh文件无法运行

   文件没有权限，chmod u+x filename

   ```python
   """
   chmod [ u / g / o / a ] [ + / - / = ] [ r / w / x ] file
   	1.u表示User，是文件的所有者；g表示跟User同Group的用户；o表示Other，即其他用户；a表示ALL，所有用户。
   	2.+表示增加权限；-表示取消权限；=表示取消之前的权限，并给予唯一的权限。
   	3.r表示Read，即读文件；w表示Write，即写文件；x表示运行文件
   """
   ```

2. yum报错

   ```python
   """
   One of the configured repositories failed (Unknown),  
    and yum doesn't have enough cached data to continue. At this point the only  
    safe thing yum can do is fail. There are a few ways to work "fix" this:  
     
     
        1. Contact the upstream for the repository and get them to fix the problem.  
     
     
        2. Reconfigure the baseurl/etc. for the repository, to point to a working  
           upstream. This is most often useful if you are using a newer  
           distribution release than is supported by the repository (and the  
           packages for the previous distribution release still work).  
     
     
        3. Disable the repository, so yum won't use it by default. Yum will then  
           just ignore the repository until you permanently enable it again or use  
           --enablerepo for temporary usage:  
     
     
               yum-config-manager --disable <repoid>  
     
     
        4. Configure the failing repository to be skipped, if it is unavailable.  
           Note that yum will try to contact the repo. when it runs most commands,  
           so will have to try and fail each time (and thus. yum will be be much  
           slower). If it is a very temporary problem though, this is often a nice  
           compromise:  
     
     
               yum-config-manager --save --setopt=<repoid>.skip_if_unavailable=true  
     
     
   Cannot find a valid baseurl for repo: base/7/x86_64
   """
   
   1.建本地源
   2.修改DNS
   ```

   3.ifconfig没有

   ```
   # 直接安装
   yum install ifconfig
   # 没有就搜索
   yum search ifconfig
   ```

   

#### 二、学习

1. 安装wegt

   ```shell
   sudo yum -y install wget
   ```

   

2. 安装epel源

   ```shell
   sudo wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
   sudo rpm -ivh epel-release-latest-7.noarch.rpm
   ```

   

3. 安装etcd

   ```
   sudo yum install etcd
   ```

4. 查看端口

   ```
   netstat -anp | grep 2380
   ```

5. 修改主机名

   ```
   hostnamectl set-hostname master.hadoop
   ```

6. rpm包的下载与安装

   ```python
   # 这里以集群配置时间同步为例
   """
   内网环境安装ntp服务主要有2种方式
   	1.没有现成的时间同步服务器，以某主机作为NTP服务器，其他主机只安装ntpdate服务，其他主机通过ntpdate命令同步时间
   	2.有现成的时间同步服务器,所有主机通过ntpdate命令同步时间
   """
   
   # 查看liunx版本
cat /etc/centos-release
   
   # 查询是否安装NTP服务
   rpm -qa | grep ntp
   
   # 根据系统版本选择rpm包
   https://pkgs.org/
       
   """
   安装rpm包 
   -i 显示套件的相关信息
   -v 显示指令执行过程
   -h 套件安装时列出标记
   显示缺乏依赖，搜索rpm安装
   """
   rpm -ivh xxxx
   
   
   ```
   
   