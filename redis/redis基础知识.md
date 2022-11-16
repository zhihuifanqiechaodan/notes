### Mac安装Redis
#### 使用Homebrew
`如果已经安装可以忽略，没有安装的执行官网命令即可安装`

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
#### 使用Homebrew安装Redis
* 安装命令

```
brew install redis
```
* 查看软件安装及配置文件位置

```
Homebrew安装的软件会默认在/usr/local/Cellar/路径下；

redis的配置文件redis.conf存放在/usr/local/etc路径下。
```
通常我们会通过配置文件启动redis,而不是直接启动redis，所以进入到`/usr/local/etc` 通过命令过滤并复制一份配置文件出来

```
// 默认端口还是6379，所以文件名为reids-6379.conf
cat redis.conf | grep -v "#" |grep -v "^$" > reids-6379.conf
```
更配配置文件中的端口号，从而达到通过多个配置文件启动多个redis

```
这里我们更改的命令有
daemonize no "更改为" daemonize yes  // 后台启动redis
logfile "" "更改为" logfile "6379.log" // 日志文件
dir "更改为" dir /usr/local/var/db/redis/  // log文件你自己想放哪里都行，自己方便。
```
* 启动redis服务

```
方法一: brew除了可以帮助我们安装软件以外，还可以帮助我们启动软件
brew services start redis

方法二: 
redis-server /usr/local/etc/redis.conf
```
* 查看redis服务进程

`我们可以通过下面命令查看redis是否正在运行`
```
ps axu | grep redis
```
* redis-cli连接redis服务

`redis默认端口号6379，默认auth为空，输入以下命令即可连接`
```
redis-cli -h 127.0.0.1 -p 6379
```
* 关闭redis服务

```
优雅的关闭 redis-cli shutdown 或者杀死 sudo pkill redis-server

或这使用 kill -s 9 xxxxx号 杀死指定服务
```
* edis.conf配置文件说明

```
redis默认是前台启动，如果我们想以守护进程的方式运行（后台运行）
可以在redis.conf中将daemonize no,修改成yes即可。
```
### Redis中数据类型介绍
Redis 数据类型(5中常用)
* string

```
String
```
* hash

```
HashMap
```
* list

```
LinkedList
```
* set

```
HashSet
```
* sorted_set

```
TreeSet
```

#### string类型的命令及介绍
1. 存储的数据：单个数据，最简单的数据存储类型，也是最常用的数据存储类型。
2. 存储数据的格式：一个存储空间保存和一个数据。
3. 存储内容：通常使用字符串，如果字符串以整数的形式展示，可以作为数字操作使用。

##### string 类型数据的基本操作
* 添加/修改数据
```
set key value
```
`注意：数据添加成功会返回 ok`
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169df7bc29e282~tplv-t2oaga2asx-image.image)
* 获取数据

```
get key
```
`注意：获取的数据如果存在会返回数据，不存在返回 null`

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e1021e16d52~tplv-t2oaga2asx-image.image)
* 删除数据

```
del key
```
`注意：删除的数据存在并且删除成功会返回 1，删除的数据不存在返回 0`

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e2574026f88~tplv-t2oaga2asx-image.image)
* 添加/修改多个数据

```
mset key1 value1 key2 value2 ...
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e63601017c2~tplv-t2oaga2asx-image.image)
* 获取多个数据

```
mget key1 key2 ...
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e70f062795a~tplv-t2oaga2asx-image.image)
* 获取数据字符个数(字符串长度)

```
strlen key
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e7e71a07462~tplv-t2oaga2asx-image.image)
* 追加信息到原始信息后部(如果原始信息存在就追加，否则就创建)

```
append key value
```
`注意： 返回值是追加后的字符串长度`
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/11/17169e9f21f2e547~tplv-t2oaga2asx-image.image)

**关于单条指令操作与多条指令操作的选择，通常考虑业务场景，数据量小一次几条数据的采用单条指令没问题，如果一次发送数据上百万上亿的。选择多条指令并要对数据进行切割，不然请求时间也是比较长的，我们将一百万条数据切割为十条十万的数据在进行多条指令存储**
****
#### hash类型的命令及介绍
`存储的困惑`

对象类数据的存储如果有较频繁的更新需求操作会显得很笨重

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716dd618f902c11~tplv-t2oaga2asx-image.image)
* 新的存储需求

```
对一系列存储的数据进行编组，方便管理，典型应用存储对象信息
```
* 需要的存储结构

```
一个存储空间保存多个键值对数据
```
* hash类型

```
底层使用哈希表结构实现数据存储
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716dd92670c3755~tplv-t2oaga2asx-image.image)
**hash存储结构优化:**
1. 如果field数量较少，存储结构优化为类数组结构
2. 如果field数量多，存储结构使用hashMa结构
##### hash类型数据的基本操作
* 添加/修改数据

```
hset key field value // 存储
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716de0bdf081441~tplv-t2oaga2asx-image.image)
* 添加/修改多个数据

```
hmset key fiedl1 value1 fiedl2 value2 ...
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716de515f381cad~tplv-t2oaga2asx-image.image)
* 获取数据

```
hget key field    // 获取指定field的值
hgettall key      // 获取所有的
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716de17351ca32b~tplv-t2oaga2asx-image.image)
* 获取多个数据

```
hmget key field1 field2 ...
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716de994198c23e~tplv-t2oaga2asx-image.image)
* 删除数据

```
hdel key field1 field2
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716de2d36d04cf2~tplv-t2oaga2asx-image.image)
* 获取哈希表中字段的数量

```
hlen key
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716deac84424ec0~tplv-t2oaga2asx-image.image)
* 获取哈希表中是否存在指定的字段

```
hexists key field  // 存在返回1，否则返回0
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716dec2fddbfdb1~tplv-t2oaga2asx-image.image)
* 获取哈希表中所有的字段名或字段值

```
hkeys key    // 获取当前表中所有的字段
hvals key    // 获取当前表中所有的字段值 
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716df34bc1169bb~tplv-t2oaga2asx-image.image)
* 设置指定字段的数值数据增加指定范围的值

```
hincrby key field increment  // 设置指定表中指定字段的数值增加increment

hincrbyfloat key field increment //设置指定表中指定字段的数值增加increment（可以为小数）
```
![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716df564c9b2adf~tplv-t2oaga2asx-image.image)

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716df5df3721bff~tplv-t2oaga2asx-image.image)
* 设置指定表中字段的数值。如果存在就什么都不做，否则就添加

```
hsetnx key field value
```

![](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2020/4/12/1716e071270a8230~tplv-t2oaga2asx-image.image)
**注意事项：**
* hash类型下的value只能存储字符串，不允许存储其他数据类型，不存在嵌套现象，如果数据未获取到，对应的值为（nil）
* 每个hash可以存储`2的32次幂 - 1`个键值对
* hash类型十分贴近对象的数据存储形式，并且可以灵活添加删除对象属性， 但hash设计初衷不是为存储大量对象而设计的，切记不可滥用，更不可将hash作为对象列表使用
* hgetall操作可以获取全部属性，但如果内部field过多，遍历整体数据效率就会很低，有可能成为数据访问瓶颈。
****
持续总结更新中