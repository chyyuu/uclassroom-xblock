部署
========

### GitLab 服务器
* 允许用户注册后不需要验证邮件即可登录  


### mooc-edx2 Docker 服务器(推荐使用CentOS 7)
* 安装docker
* 创建密钥
  编辑脚本`scripts/pemgen.sh`，设置环境变量`UC_DOMAIN`为Docker服务器的域名  
  执行脚本`scripts/pemgen.sh`，然后将会在`scripts/certs`文件夹下看到生成的若干密钥
* 使用以下命令，

1 stop early docker server

```
sudo service docker stop
```

2 启动Docker服务：  
```
$sudo docker -d --tlsverify --tlscacert=<ca.pem> --tlscert=<server-cert.pem> --tlskey=<server-key.pem> -H=0.0.0.0:2376
OR
cd ~; rundocker.sh
```

below two steps is needless now.

* 访问Docker服务器，添加默认镜像，在`scripts`目录下找到Dockerfile文件，执行： 
```
$sudo docker --tlsverify -H=<docker-server>:<port> --tlscacert=<ca.pem> --tlscert=<cert.pem> --tlskey=<key.pem> pull docker.io/fedora:21
```
```
$sudo docker --tlsverify -H=<docker-server>:<port> --tlscacert=<ca.pem> --tlscert=<cert.pem> --tlskey=<key.pem> build --rm -t uclassroom/ucore-vnc-base .
```


### Node.js 服务器(推荐使用CentOS 7)
* 安装 `node.js` 和 `npm`：  
```
$sudo yum install nodejs, npm
```  
  ，或者  
```
$sudo apt-get install nodejs, npm
```  
* 进入 `rtc_node` 文件夹下， 执行以下命令，安装子模块：  
```
$npm install
```  
* 编辑文件 `config.json`， 其默认参数为：  
```
{
"PORT": 1986,
"MAX_USER_NUM_OF_ROOM": 2
}
```

3 启动Node.js监听服务
```
$node app.js
OR
cd ~; runnode.sh

```

### mooc-edx2  Open edX 服务器(使用Ubuntu Server 12.04)
* Open edX安装方法请参考[部署文档](https://github.com/ggxx/code-viewer/blob/master/%E9%83%A8%E7%BD%B2%E6%96%87%E6%A1%A3.md)或[部署文档](https://github.com/xyongcn/code-viewer/blob/master/%E9%83%A8%E7%BD%B2%E6%96%87%E6%A1%A3.md)  
* 在Open edX服务器上安装docker，请参考[这里](http://docs.docker.com/installation/ubuntulinux/#ubuntu-precise-1204-lts-64-bit)  


### 在Open edX服务器安装XBlock uc_rtc
* 编辑文件 `uc_rtc/uc_rtc/static/js/src/static.js`， 设置全局变量 `SOCKET_IO_URL`：  
```
var SOCKET_IO_URL = 'http://<node.server.host>:<port>';
```
* 执行以下命令安装 uc_rtc XBlock：  
```
$sudo -u edxapp /edx/bin/pip.edxapp install <path_of_uc_rtc>
```
* 重启edxapp服务:  
```
$sudo /edx/bin/supervisorctl -c /edx/etc/supervisord.conf restart edxapp:
```


### 在Open edX服务器安装XBlock uc_docker
* 编辑文件 `uc_docker/uc_docker/config.py`, 配置参数
* 执行以下命令安装 uc_docker XBlock：  
```
$sudo -u edxapp /edx/bin/pip.edxapp install <path_of_uc_docker>
```
* 重启edxapp服务:  
```
$sudo /edx/bin/supervisorctl -c /edx/etc/supervisord.conf restart edxapp:
```

### 备注
* 所有的 `<var>` 取决于你自己的服务器环境
* 卸载 uc_rtc 和 uc_docker的命令是：  
```
$sudo -u edxapp /edx/bin/pip.edxapp uninstall uc-rtc-xblock
```  
  ，和
```
$sudo -u edxapp /edx/bin/pip.edxapp uninstall uc-docker-xblock
```


#### 直接激活注册用户
```
sudo mysql
> use edxapp;
> update auth_user set is_active=1 where username="XXX"
```
#### 修改后台
```
firefox http://192.168.122.135:18010/
```
进入 edx studio

#### 创建uc_docker组件出错1
在mooc_edx2机器中的“/tmp”目录下，执行
```
cd /tmp
mkdir uc_docker
chmod 755 uc_docker
```
#### 创建uc_docker组件出错2
如果出现其他错误，有可能是git server 没起或者git server的ip变了
目前的git server ip是 192.168.122.5

#### 创建uc_docker组件出错3
前面对错误1的处理不够全面，应该是
```
chmod 777 uc_docker
```
#### 创建uc_docker组件出错(未知)
可以查看 edx的后台日志
```
/edx/var/log/cms/edx.log
```
可以查看 edx的前台日志
```
/edx/var/log/lms/edx.log
```
