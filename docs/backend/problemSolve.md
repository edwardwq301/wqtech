## 阿里云

因为机器（联想小新AIR14-2021）承受了不能承受之重（make qemu直接卡住了），转战阿里云，阿里云做两个实验后可以免费领取7个月服务器（实验内容很简单，40min两个全能搞定），非常友好了

??? "发点牢骚"

    腾讯云也不错，30块3个月，也有学生认证获取更长时间的免费服务器，[链接🔗](https://cloud.tencent.com/act/campus)

    相比之下华为云就不是非常给力了，老师天天在学校里搞宣传，但是不给广大的穷哥们整点福利（点名批评 :triumph: ）

    |kind|details|
    |:--|:--|
    |处理器|AMD Ryzen 5 5500U with Radeon Graphics 六核|
    |主板|联想 LNVNB161216|
    |内存|镁光16GB DDR4 3200MHz （ 8GB ＋ 8GB )|
    |显卡|AMD Radeon（TM） Graphics （512MB／联想)|
    |显示器|LEN140FHD［联想LEN9023］（13.9英寸）|
    


## 虚拟机

### vpn

- virtual box 版本 7.0.10 r158379 (Qt5.15.2)
- Ubuntu 20.04 清华源镜像

1. 我的是先把网桥和nat全都打开
    - 这一步只开nat ==或者== 网桥都行，关于nat和网桥请查询专业知识（我的机器二选一都可以）
2. 在 `~/.config/clash` 配置 `config.yaml` 和 `Country.mmdb`
    1. `config.yaml` 里添加服务商的订阅
    2. `Country.mmdb` [链接](https://github.com/Dreamacro/maxmind-geoip/releases)
3. 按 `config.yaml` 修改网络代理
    1. http proxy,https proxy,ftp proxy,填写对应端口
    2. socks host 填写对应端口
    3. 主机全写 `127.0.0.1`
4. 运行clash `./clash` 
   
### 连不上网

??? "tips"
    直接用Oracle的virtual box就好，更加方便

---

- 最开始装Ubuntu联网没问题，重启后也没问题
- 2023/07重装之后就连不上网，网上教程也没解决，后来发现重装网络部分就能连上
- [useful guide](https://zhuanlan.zhihu.com/p/130984945)

??? "重装虚拟网络编辑器"

    vmware playstations pro，编辑，虚拟网络编辑器，更改设置，还原为默认设置

## 数据库

### MySQL安装后连接不上

背景：**Windows**重装MySQL并且立刻进入workbench后能正常数据库使用，但是重启或关机再开就连接不上了。

!!! tip "the path already exists"
    比如安装server我要装到`D:\MYSQL\server`，提示我`the path already exists`，那就把自己建的server文件夹删掉，填写`D:\MYSQL`即可

**可能的解决方案：**

终端（powershell）输入`& 'D:\MYSQL\server\bin\mysqld.exe' --defaults-file="D:\MYSQL\data\my.ini" `   ==别忘了切换成对应的文件夹==

为什么这么做呢？

这条命令的意思是把`D:\MYSQL\data\my.ini`的内容送给`D:\MYSQL\server\bin\mysqld.exe`执行。
我看网上有`net start mysql`但是我按照相关教程成功启动mysql后，workbench还是不能连接。后来又在StackOverflow[^1]上看到启动`MySQL57` 

我也做类似步骤，启动了`MySQL80`(因为我的配置里它就叫这个名字，对应下面的图中windows servers name),然后workbench成功连接👍

接下来再接再厉，在`services.msc`发现它其实就是相当于执行`D:\MYSQL\server\bin\mysqld.exe' --defaults-file="D:\MYSQL\data\my.ini`这条指令，但是我看workbench里的连接配置就是这个意思，但是不太明白为什么workbench没有成功，希望有高人指点。
![workbench](../image/sqlconfig.png)

   
[^1]: [对应的StackOverflow问题传送门](https://stackoverflow.com/a/37612256/21587403)
