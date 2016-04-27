# 版本号：v1.0

这是构建并发布的第一个版本，可以称为基础版本，提供了开箱即用的视频分享系统，下面讲一下这个版本的特性和缺陷。

## 特性

* 前台汉化
* 视频分享系统的基本功能，上传，播放等。
* 替换官网代码中的Google字体、ajax等类库，链接改为360的前端镜像，加速访问速度。
* 替换视频播放页sharethis的分享为百度分享。

## 不足

* 汉化不完全，因为这个官方的模板一些短语是直接写在代码中的，语言包不好做，另外个人时间有限，所以目前的汉化率在80%
* 后台管理完全没有汉化，原因同上，时间有限

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