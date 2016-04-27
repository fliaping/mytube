# mytube
a video sharing system base on docker and clipbucket

基于docker和 [clipbucket](http://clip-bucket.com/) 的视频分享系统,其实就是把clipbucket安装在docker上，并做了汉化和修改。

当前版本v1.0，有关特性和不足请看这里 [v1.0](https://github.com/paynexu/mytube/tree/master/docker/v1.0)

这里简要说明如何使用，**详细的使用方法请阅读我的博客文章**

[基于Docker的开源视频分享系统解决方案](http://blog.xvping.cn/2016/04/24/a-solution-of-open-source-video-sharing-system-based-on-docker/)

# clipbucket概述

这个视频系统属于LAMP的技术栈的产品，主要是PHP代码构建的CMS系统，和利用PHP-CLI来执行bash命令调用转码软件。转码用到的软件有

* ffmpeg，大名鼎鼎的音视频软件，各种格式编解码、转码、录制、编辑等。
* flvtool2，因为ffmpeg对flv格式的视频支持不太好，所以需要这货
* imagemagick，看名字就知道这是处理图像用的，在这个系统中我们用它来获取视频截图。
* MP4Box(gpac)，转码的时候，ffmpeg先对视频解码，然后再编码成H264视频流和AAC音频流（具体格式可调）。HTML5默认支持MP4格式视频播放，所以还需要把音视频流打包成MP4文件，这个软件就干了这事。

# 当Clipbucket遇上Docker

Docker的详细安装教程在我前面提到的博客中，没用过的请看我的博客。

## 下载镜像
镜像大小为1.3G

国内下载点

```bash
# Daocloud下载点
docker pull daocloud.io/xuping/clipbucket:v1.0

# 灵雀云下载点
sudo docker pull index.alauda.cn/xuping/clipbucket:v1.0
```
国外下载点

```bash
# docker hub 下载点
sudo docker pull xuping/clipbucket:v1.0

```
查看下载下来的镜像

```bash
docker images
```

## 镜像相关信息

安装的软件：除了之前列的视频转码相关软件，还有apache2、MariaDB、php5、php5的一些插件

* root密码：123
* mysql root密码：123,关闭root远程登录
* clipbucket使用的数据库：clipbucket
* clipbucket管理员账户：admin:123
* apache默认目录： /var/www/html
* php.ini 配置如下：

```bash
upload_max_filesize = 1024M
max_execution_time = 7300
max_input_time = 3000
memory_limit = 512M
magic_quotes_gpc = on
magic_quotes_runtime = off
post_max_size = 1024M
output_buffering = off
display_errors = on
```
## 运行镜像

查看下载下来的镜像

```bash
sudo docker images
```
运行镜像

```bash
docker run --restart=always -d -p 8081:80 -p 2222:22 -v /data:/data xuping/clipbucket:v1.0  /usr/bin/supervisord 
```
如果是从国内站点下载的，镜像名字前面要加域名，例如```daocloud.io/xuping/clipbucket:v1.0```

下面解释下参数的意义

* --restart=always 使容器随docker启动自动运行
* -d 以Detached模式运行容器
* -p 8081:80 -p 2222:22 端口映射，将容器的22端口映射到宿主机2222端口，80端口映射到8081端口
* -v /data:/data 挂载宿主机的 /data 目录到容器中的 /data 目录
* xuping/clipbucket:v1.0 是我镜像的名字
* /usr/bin/supervisord 容器启动时运行的程序，这是一个守护进程，确保该启动的软件都运行着，这由于docker的另外一个特性。

## 将数据文件存在宿主机中

前面说过，要持久化数据，最好将数据存在宿主机。下面的这些其实通过Dockerfile或者写在一个启动脚本中就可以的，但这个版本还没实现，后续版本更新会通过Dockerfile来进行。

```bash
# 因为已经挂载宿主机的/data目录到容器的/data目录，
# 所以可以认为当前/data目录就是主机的/data目录

# 数据库文件转移
cp -r /var/lib/mysql/clipbucket   /data
rm -r /var/lib/mysql/clipbucket/
ln -s /data/clipbucket/ /var/lib/mysql

# 视频文件转移
cp -r /var/www/html/files /data
rm -r /var/www/html/files
ln -s /data/files/ /var/www/html/
ls -l /var/www/html/files
```
## 修改密码

为了方便，默认的密码比较简单，在你部署好镜像之后一定要**修改密码**，需要修改的有系统的root密码，mysql的root密码。

```bash
# 修改系统root密码
passwd root

# 修改mysql的root密码
# 登陆mysql,默认密码123
mysql -uroot -p

mysql> use mysql;
mysql> update user set password=PASSWORD("你的密码") where User='root';
mysql> flush privileges;

```
修改clipbucket配置文件中的mysql密码

```bash
vi /var/www/html/includes/dbconnect.php

# 修改$DBPASS = '123';中的123为你的密码。
```

# 更多内容请阅读博客
[基于Docker的开源视频分享系统解决方案](http://blog.xvping.cn/2016/04/24/a-solution-of-open-source-video-sharing-system-based-on-docker/)