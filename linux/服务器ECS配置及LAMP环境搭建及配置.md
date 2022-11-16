说起做一个阿里服务器ECS配置文章也是因为买了一个服务器好几年了, 一直没有好好玩过, 基本也就是自己放一些node项目和vue项目展示用, 今天初始化一下服务器,重新配置一番,顺便做个新手笔记给大家参考
## 服务器配置
这是我自己的服务器配置, 自己玩是足够了
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85e65bf85f744~tplv-t2oaga2asx-image.image)
## ECS云服务器配置及环境
* 登录阿里云服务器[官网](https://www.aliyun.com/?utm_medium=text&utm_source=bdbrand&utm_campaign=bdbrand&utm_content=se_32492&accounttraceid=07f8d1b7-6256-4907-a549-913d71b2537c)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85ed4752e50ac~tplv-t2oaga2asx-image.image)
* 左侧导航 => 控制台 

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85e8478576117~tplv-t2oaga2asx-image.image)
* 进入云服务器ECS

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85ee466409db4~tplv-t2oaga2asx-image.image)
* 进入实例

```
选择你当时购买的服务器机房
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85f014adcddf4~tplv-t2oaga2asx-image.image)
* 管理

```
- 停止（关机）
- 才能切换系统
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85f10cd6ba7d4~tplv-t2oaga2asx-image.image)
* 返回实例,找到更多 => 磁盘和镜像 => 更换系统盘


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85f51726b8379~tplv-t2oaga2asx-image.image)

* 公共镜像 (镜像和我一样哈, 不然会有一些稍微的不同)

```
centOS 7.2 64
```
* 设置密码

```
- 大家字母 + 小写字母 + 数字
- 尽量取个简单点，方便记
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85f4a1a29303a~tplv-t2oaga2asx-image.image)
* 系统启动成功完成之后，所有配置都是在命令行中操作
## putty软件连接 ECS服务器
软件我上传到了[百度网盘](https://pan.baidu.com/s/18s_9nHFHBdWDzaDMMQQ81A)开箱即用,体积不到1M, 当然也有其他的软件请自行百度

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d86015d34f6fc2~tplv-t2oaga2asx-image.image)
* IP

```
你服务器的公网IP
```
* 端口

```
22
```
* 连接类型

```
ssh
```
* 登录

```
- 第一次会生成密钥
- 自己个人电脑 是
- 公用电脑 否
```
* putty命令行

```
- root
- 密码 （切换系统的时候设置的密码）
- 输入的时候没有提示，直接输入
- 就能进入服务器
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d8644b30a63722~tplv-t2oaga2asx-image.image)
* 远程连接(阿里云官网提供的)

```
第一次会弹窗 让你记录下密码，很重要，保存到电脑上，会经常用到
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d8607d32622b00~tplv-t2oaga2asx-image.image)
## Node 环境搭建
windows系统的电脑搭建Node环境是很简单的,服务器也一样
* 下载FileZilla
```
方便直接创建目录上传文件等等
```
* 登录站点

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d860f31da5db8f~tplv-t2oaga2asx-image.image)
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d860e931d89bd0~tplv-t2oaga2asx-image.image)
* 新建一个文件来管理
```
我新建了一个tomato文件专门来存放我配置的东西方便自己查找操作
```
* 打开putty命令行

```
cd /                进入根目录
ls                  查看目录
cd tomato/node/     进入我刚创建的tomato文件下的node文件夹
wget https://nodejs.org/dist/v12.11.0/node-v12.11.0-linux-x64.tar.gz    下载node压缩包
tar xvf node-v12.11.0-linux-x64.tar.gz                                  解压node压缩包
rm -rf node-v12.11.0-linux-x64.tar.gz                                   删除node压缩包
cd node-v12.11.0-linux-x64/bin                            ls查看目录，你会看到 node npm
```
**设置全局(环境变量),在任何目录都可能运行 node 和 npm 命令**

```
ln -s /tomato/node/node-v12.11.0-linux-x64/bin/node /usr/local/bin/node
ln -s /tomato/node/node-v12.11.0-linux-x64/bin/npm /usr/local/bin/npm
```
完成上述配置,即可在任何目录运行node和npm命令了
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d862090c447192~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d86211ea00527c~tplv-t2oaga2asx-image.image)
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d861c83eba7d44~tplv-t2oaga2asx-image.image)
## 利用FileZilla上传项目

```
- 主机
  - 公网ip
- 端口
  - 22
- 协议
  - ssh
- 登录类型
  - 正常
- 用户名
  - root
- 密码
  - 你的密码
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d860f31da5db8f~tplv-t2oaga2asx-image.image)

```
写一个js,用node运行起来,然后通过公网ip的形式访问, 这里我写了一个简单的接口文件
```
http.js

```
const http = require('http')
const request = require('http').request
const fs = require('fs')

// 代理跨域
const fn = response => {
    const options = {
        host: "localhost",
        port: 4001,
        method: "get",
        path: "/"
    }
    //执行就发起请求了
    const req = request(options, res => {
        // console.log(res);
        let data = {}
        res.setEncoding("utf8")
        res.on("data", (chunk) => {
            console.log(`响应主体:${chunk}`);
            // 将data赋值为第三方代理请求回来的数据
            data = chunk
        })
        res.on("end", (chunk) => {
            response.write(data)
            response.end()
        })
    })
    // 监听报错
    req.on("error", err => {
        console.log(err);
    })
    req.write("")
    req.end()
}

const server = http.createServer((request, response) => {
    // 和请求相关的信息都在 request 里面
    // 后台给客户端返回的数据, 都在 response 里面

    // CORS跨域  设置允许跨域
    response.setHeader("Access-Control-Allow-Origin", "*")

    // 设置响应头, 第一个参数 http 状态码, 第二个参数是一个对象
    // response.writeHead(2010, {
    //     "Content-Type": "text/pligin;charset=utf-8" //设置内容的类型
    // })
    // response.write()可以调用多次
    // response.write()0向客户端返回数据, 这里返回的中文会乱码,因此需要设置响应头
    // response.write("向客户端返回数据~~~")
    // response.write(request.url) // 请求路径
    // response.write(request.method) // 请求方式
    // response.write(JSON.stringify(request.headers)) // 请求头信息

    // response.end()结束响应, 不然会一直处于响应状态不结束, 只能调用一次
    // response.end("~~~我是response.end()返回的数据") // end也可以返回数据, 必须传字符串

    if (request.method === "GET") {
        response.writeHead(200, {
            "Content-Type": "text/html;charset=utf-8"
        })
        switch (request.url) {
            case "/":
                // response.write(fs.readFileSync("../demo/index.html", "utf8")) // 同步执行
                response.write(JSON.stringify({
                    a: 1,
                    b: 2
                })) // 同步执行
                response.end()
                break;
            case "/home":
                //home
                fs.readFile("../demo/index.html", "utf8", (err, data) => { // 异步执行
                    response.write(data)
                    response.end()
                })
                break
            case "/request": //代理跨域
                fn(response)
                break
            default:
                // 404
                response.write("<h1>404页面<h1>")
                response.end()
                break;
        }
    }
    // response.end()
    // 配置请求
})
// 监听6300端口
server.listen(6300, () => {
    console.log("服务监听在6300端口");
})
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d862d92e35d6e6~tplv-t2oaga2asx-image.image)
将写好的http.js文件放到nodeDemo文件中运行

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d8633e01511e9d~tplv-t2oaga2asx-image.image)
**然后通过公网ip的形式访问，会发现访问不了，原因是没有开放端口**
## 安全组开放访问端口

```
- 进入实例
- 安全组
- 配置规则
- 添加安全规则
- 协议类型 TCP
  - 80/80
  - 6000/9999
  - 443/443
- 协议类型 HTTP/HTTPS
  - 80/80
- 授权类型：地址段访问
- 授权对象
  - 0.0.0.0/0
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d86345ee01c124~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d863499800c035~tplv-t2oaga2asx-image.image)
这时候通过公网ip的形式访问就能拿到数据了


![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d86473e84642a7~tplv-t2oaga2asx-image.image)
## 域名绑定服务器
* 在阿里云购买域名

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d863ef01636180~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d863fcc2d48a00~tplv-t2oaga2asx-image.image)
* 通过域名访问刚刚的node项目

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d8641369906267~tplv-t2oaga2asx-image.image)
## 域名需要备案

```
只需要进入备案专区
按照流程操作, 官方有流程图,就可以备案成功
备案成功的时间大概最快需要15天左右, 慢的话需要一个月
备案通过了,才能够使用域名访问虚拟主机的项目
```
## LAMP环境搭建及配置

```
linux + apache + mysql + php

时间较长

服务器都可以用命令操作的

默认是没有图形界面的，但是可以自个百度 安装图形界面 

centOS 图形界面

```
[来自阿里云官方论坛的文档](https://help.aliyun.com/document_detail/50774.html?spm=a2c4g.11186623.6.766.1fXGjs)按照这个文档配置就可以了

LAMP指Linux+Apache+MySQL/MariaDB+Perl/PHP/Python，是一组常用来搭建动态网站或者服务器的开源软件。它们本身都是各自独立的程序，但是因为常被放在一起使用，拥有了越来越高的兼容度，共同组成了一个强大的Web应用程序平台。
## 常用命令

```
// ECS服务器
cat /etc/redhat-release     // 查看系统版本
systemctl status firewalld  // 命令查看当前防火墙的状态
systemctl stop firewalld    // 临时关闭防火墙,重启Linux后,防火墙还会开启
ystemctl disable firewalld  // 永久关闭防火墙

// Apache
httpd -v                    // 查看Apache的版本号
systemctl start httpd       // 启动Apache服务
systemctl restart httpd     // 重启Apache服务
systemctl enable httpd      // 开机自启动Apache服务

// MySQL
systemctl start mysql       // 启动MySQL服务
systemctl enable mysql      // 开机自启动MySQL服务
mysqladmin -u root password // 修改MySQL的root用户密码
mysql -uroot -p             // 登录MySQL数据库
\q                          // 退出MySQL
```
## 阿里云ECS服务器优惠码/[优惠卷地址](https://promotion.aliyun.com/ntms/yunparter/invite.html?userCode=1ysggcln)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2019/10/1/16d85e0f69694ec5~tplv-t2oaga2asx-image.image)
## 云服务器ECS 系列相关文章
[阿里云服务器ECS配置及LAMP环境搭建及配置(新手攻略第一弹)](https://juejin.im/post/6844903956624179207)

[阿里云服务器配置Jenkins自动打包部署vue项目(新手攻略第二弹)](https://juejin.im/post/6844903956829700110)