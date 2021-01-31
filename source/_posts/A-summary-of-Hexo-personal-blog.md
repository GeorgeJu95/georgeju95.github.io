---
title: Hexo+Github建立个人博客的一点总结
date: 2019-06-30 19:03:00
tags: Hexo
reward: true
---

## 一、环境准备
以Mac Os系统为例：
### 1、安装nodejs（去官网下载LTS版本）和 git
确保安装成功 node -v 和 npm -v 测试一下
<!-- more -->
### 2、终端进入管理员模式 
``` bash
$ sudo su
```
### 3、npm安装cnpm(针对国内用户，淘宝有镜像站)
``` bash
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```
### 4、cnpm安装hexo
``` bash
$ cnpm install -g hexo-cli
```
### 5、新建一个文件夹，这个文件夹就是博客的全部内容存储的地方。cd进入这个目录
### 6、初始化hexo框架
``` bash
$ hexo init
```
### 7、启动博客。出现一个本地端口地址，粘贴到浏览器即可
``` bash
$ hexo s
```

---


## 二、写博客

### 1、新建一篇文章：hexo n "文章名(不需要.md扩展名)"
之后会在source/_posts中看到这个文件
### 2、写好后进行清理、生成、预览
``` bash
$ hexo clean
$ hexo g
$ hexo s
```



---



## 三、部署到远端服务器（可以免费部署到GitHub）

### 1、github新建一个仓库，名字一定命名为：昵称.github.io
### 2、在创建的文件夹下安装一个git部署的插件
``` bash
$ cnpm install hexo-deployer-git --save
```
### 3、对_config.yml进行配置，配置git到username.github.io，这样可以在本地写完后一键部署
可以使用vim打开，到文件的最底部添加
deploy:
&emsp;type: git
&emsp;repo: 复制上你创建的github仓库地址
&emsp;branch: master
### 4、部署到远端
``` bash
$ hexo d
```
输入github账号和密码。推成功后刷新github仓库发现有所变化，此时可以访问username.github.io

---


## 四、更换主题

### 美化hexo，在github上找到hexo-theme-yilia框架，git clone到本地的博客文件夹下的themes文件夹中，更改配置文件的主题为yilia就搞定了。
``` bash
$ git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia
$ vim _config.yml
```
theme: landscape --> theme: yilia
``` bash
$ hexo clean
$ hexo g
$ hexo s
```
退出并推送到远端：
``` bash
$ hexo d 
```

---


## 五、一些坑

### 微信分享异常
主文件夹下yml把url设置正确，再使用//api.qrserver.com/v1/create-qr-code/?size=150x150&data= 或者 http://qr.liantu.com/api.php?text= 修改themes\yilia\layout\\_partial\post\share.ejs即可。注意post中的md文档命名只能是英文，而且不能有空格等特殊字符。

### 对整个流程较为熟悉后：
* <mark>备份与恢复</mark>——参考下面第三条链接
* 使用<mark>SSH连接Github</mark>——参考第四条链接
* 添加<mark>Valine评论系统</mark>——参考第五条链接
* <mark>给文章增加目录</mark>(博客增加相册功能也可参考该博主的文章)——参考第六条链接
* <mark>Hexo与Mathjax的冲突及解决方案</mark>——参考第七条链接
* <mark>关于备份其他主题到远程仓库是灰色文件夹的解决办法</mark>——参考第八条链接
* 使用<mark> yilia-plus </mark>后仍想使用<mark> Valine 评论系统 </mark>——参考第九条链接
* 推送到<mark> Git 仓库配置 SSH</mark>——参考第十条链接

---


## 六、参考内容

1. https://www.bilibili.com/video/av44544186
2. http://dongshuyan.com/2016/04/07/%E6%80%8E%E4%B9%88%E5%86%99%E5%8D%9A%E5%AE%A2/
3. https://meetes.top/2018/03/01/Hexo%E5%A4%87%E4%BB%BD%E4%B8%8E%E6%81%A2%E5%A4%8D/
4. https://help.github.com/en/articles/connecting-to-github-with-ssh
5. https://blog.xxwhite.com/2017/Valine.html
6. http://lawlite.me/2017/04/17/Hexo-yilia%E4%B8%BB%E9%A2%98%E5%AE%9E%E7%8E%B0%E6%96%87%E7%AB%A0%E7%9B%AE%E5%BD%95%E5%92%8C%E6%B7%BB%E5%8A%A0%E8%A7%86%E9%A2%91/
7. https://segmentfault.com/a/1190000007261752
8. https://swibinchter.github.io/2017/01/11/Hexo%E5%8D%9A%E5%AE%A2%E4%B8%BB%E9%A2%98%E6%96%87%E4%BB%B6%E5%A4%B9%E6%97%A0%E6%B3%95%E5%AE%8C%E6%95%B4push%E5%8F%8Aclone/
9. https://blog.csdn.net/weixin_44330881/article/details/102819061
10. https://fzy15116089232.blog.csdn.net/article/details/111537943




