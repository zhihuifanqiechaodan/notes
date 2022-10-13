`Jenkins是直接部署到阿里云服务器ECS上的, 服务器系统:  CentOS  7.2 64位`

## 第一步: 服务器安装Java
jenkins是运行在java环境中的,所以要先安装java,配置java环境变量后才能使用。
* 卸载系统自带的jdk

```
// 查找系统jdk 
rpm -qa|grep java 

// 如果查找到了 先全部卸载了在重新安装
rpm -e --allmatches --nodeps java包名
// 例如
rpm -e --allmatches --nodeps java-1.8.0-openjdk-1.8.0.212.b04-0.el7_6.x86_64

//检查是否卸载干净
[root@VM_0_2_centos ~]#  rpm -qa|grep java 
```
* 查找yum下可更新的Java列表

```
yum -y list java*
//或者
yum search jdk
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b5e3dc057f98~tplv-t2oaga2asx-image.image)
* 安装java

```
yum install -y java-1.8.0-openjdk.x86_64
//验证完成安装
java -version
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b5ebadfc1aeb~tplv-t2oaga2asx-image.image)
* 配置环境变量

```
// 打开文件
vi /etc/profile  

// i 进入编辑模式
// 文件末尾加入以下内容
export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar

// 点击键盘ESC键 输入 :wq // 退出并保存
```

```
//使配置文件生效
source /etc/profile 
source ~/.bash_profile
//或重启机器配置生效
reboot
```
## 服务器安装Jenkins
* 检查是否安装好Java

```
java -version // 如果没有出现版本号请按照上述步骤重新安装
```
* 获取jenkins安装源文件   

```
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
```
* 导入公钥 (如果报错，多执行几次就好了) 

```
yum -y update nss
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```
* 安装Jenkins

```
yum install -y jenkins   // 看网速需要等待一会
```
* 配置文件修改( 默认端口为8080) 

```
vim /etc/sysconfig/jenkins
// 修改了默认端口为 8888
// 修改了用户名为 root
// 如果没被占用你可以不改
```
* 启动Jenkins

```
 service jenkins start
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b70d41872425~tplv-t2oaga2asx-image.image)
## 启动 jenkins
* 浏览器输入 http://ip:8888， ip:服务器外网ip地址 例：http://47.16.213.xxx:8888

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b72fa85c57f2~tplv-t2oaga2asx-image.image)
* 等待一会之后 提示你输入管理员密码

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b73488cd5074~tplv-t2oaga2asx-image.image)

```
// 打开服务器输入上述的命令
vi /var/lib/jenkins/secrets/initialAdminPassword
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b758ae7a43c6~tplv-t2oaga2asx-image.image)
复制管里面密码到页面
* 安装插件

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b76b378190e7~tplv-t2oaga2asx-image.image)
* 点击推荐安装,稍等片刻，会出现

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b774cb01d6f8~tplv-t2oaga2asx-image.image)

```
这个时候安装的的插件会比较多，耗时有点久。耐心等待。
安装完插件之后 创建第一个管理员用户
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b77c1ec753af~tplv-t2oaga2asx-image.image)
继续点击保存并完成

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b782989fa4b4~tplv-t2oaga2asx-image.image)
点击开始使用 jenkins 这个时候 jenkins就已经配置成功了。
## Jenkins创建一个构建任务

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b794bc279d86~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b79e3ae8d81a~tplv-t2oaga2asx-image.image)
这里我代码仓库用的是GitHub(码云也一样的)

输入仓库地址。因为仓库是私有的所以会有报错提示 这里要添加Credentials。就是你码云或者github账号。


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b7dddc84d08d~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b7d433a4ae1d~tplv-t2oaga2asx-image.image)
这里可以填一下要构建的分支

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b7ea441be540~tplv-t2oaga2asx-image.image)
回到首页 ==> 就会看到一个 tomato-admin 的构建任务

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b7f69a49cf78~tplv-t2oaga2asx-image.image)
点击立即构建

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b812e6d47099~tplv-t2oaga2asx-image.image)
jenkins构建任务已经完成
## 配置 Jenkins 构建时执行的shell脚本
点击配置
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8246feb3c32~tplv-t2oaga2asx-image.image)
点击增加构建步骤
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b839d5ce60fc~tplv-t2oaga2asx-image.image)
点击执行shell
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b83d98996849~tplv-t2oaga2asx-image.image)
输入shell命令
```
// 下载工作区npm依赖包
npm install
// 删除dist目录下的所有文件,dist目录即为当前jenkins工作区打包后的文件
rm -rf ./dist/*
// 执行打包命令
npm run build
// 删除服务器上/usr/local/apache2/htdocs/tomato文件夹下的所有文件
rm -rf /usr/local/apache2/htdocs/tomato/*
// 把当前构建工作区dist目录里的文件 copy 到服务器/usr/local/apache2/htdocs/tomato文件夹下
cp -rf ./dist/* /usr/local/apache2/htdocs/tomato
```
保存后点击立即构建吗, 发现构建报错了(红色圆点即为构建失败,蓝色成功)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b892a6046479~tplv-t2oaga2asx-image.image)
点击进入此次构建详情 => 点击控制台输出 => 查看报错信息

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8a64a465cc7~tplv-t2oaga2asx-image.image)
**Jenkins默认是没有安装node插件的,所有没有npm命令**
手动安装node插件

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8b255eb3f84~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8bc6ea08dbd~tplv-t2oaga2asx-image.image)
安装成功后点击全局工具配置

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8c53e862b19~tplv-t2oaga2asx-image.image)
新增NodeJS

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8ce6d332d14~tplv-t2oaga2asx-image.image)
**返回tomato-admin配置空间,点击构建环境**

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b8ddf5fa7e1b~tplv-t2oaga2asx-image.image)
保存后点击立即构建 第一次构建 会执行 npm install 下载很多包 会很慢

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b901d635d87e~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b90611646ccd~tplv-t2oaga2asx-image.image)
这样就构建成功了 通过域名或者浏览器去访问文件夹名称即可

```
// http://zhihuifanqiechaodan.com/tomato
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/2/16d8b982ef80de9b~tplv-t2oaga2asx-image.image)
## 云服务器ECS 系列相关文章
[阿里云服务器ECS配置及LAMP环境搭建及配置(新手攻略第一弹)](https://juejin.im/post/6844903956624179207)