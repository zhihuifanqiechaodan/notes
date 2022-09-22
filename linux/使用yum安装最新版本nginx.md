### 安装nginx

* 安装yum-utils
```js
yum install yum-utils
```
* 添加nginx最新版本源

创建文件/etc/yum/repos.d/nginx.repo
```js
vim /etc/yum.repos.d/nginx.repo
```
添加以下内容
```js
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
```
*  查看nginx源
```js
yum info nginx
```

* 安装nginx
```js
yum install nginx
```

* 查看版本
```js
nginx -v
```

* 启动服务
```js
systemctl start nginx
```

* 添加开机启动
```js
systemctl enable nginx
```

* 部分命令
```js
# 停止服务
systemctl stop   nginx
#查看服务状态
systemctl status nginx
#启动服务
systemctl start  nginx
#添加开机启动
systemctl enable nginx
# 查看开机启动服务
systemctl list-unit-files |grep enable
```

使用yum安装nginx路径在/usr/sbin/nginx, 配置文件在/etc/nginx