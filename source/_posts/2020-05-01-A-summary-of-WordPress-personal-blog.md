---
title: WordPress-Nginx-Docker 搭建博客
date: 2020-05-01 16:53:00
tags: WordPress
toc: true
reward: true
---

## 需要条件
* 购买一个云服务器（ 本人使用的是阿里云的 ECS ）
* 购买一个域名并解析域名（ cn 域名需要一个月左右的备案时间 ）
* 准备远程 SSH 连接软件 [Putty](https://www.chiark.greenend.org.uk/~sgtatham/putty/ "Putty") 以及 SFTP 软件 [filezilla 客户端](https://filezilla-project.org/ "filezilla 客户端")

<!-- more -->

## 部署过程
* **远程连接上服务器，为了方便，系统我使用的 Ubuntu 18，给终端装上 zsh 工具**
```bash
apt-get update
apt-get install git 

# 安装zsh 和 oh my zsh ( 通过 curl )
sudo apt-get install zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

# 安装插件
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# 配置用户主题和插件选项
vim ~/.zshrc
# 更改主题。ZSH_THEME="robbyrussell" 改为 ZSH_THEME="ys"
# 更改插件设置, x 命令解压一切文件，z命令快速跳转目录，google 直接搜索内容。
# plugins=(git) 改为 plugins=(git z extract web-search zsh-syntax-highlighting zsh-autosuggestions)

# 更新设置
source ~/.zshrc
```

* **安装 Docker**
```bash
// 把 Docker CE 的稳定(stable)版本安装在系统中。
curl -fsSL get.docker.com -o get-docker.sh
sudo sh get-docker.sh --mirror Aliyun
// 启动 Docker CE
sudo systemctl enable docker
sudo systemctl start docker
// Docker Compose 存放在 GitHub，不太稳定，使用国内镜像
curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
// 国内镜像源加速
curl -sSL https://get.daocloud.io/daotools/set_mirror.sh | sh -s http://f1361db2.m.daocloud.io
// 修改后重启服务
systemctl daemon-reload
systemctl restart docker
```

* **克隆远程仓库，开始部署**
```bash
git clone https://github.com/mjstealey/wordpress-nginx-docker

# 修改配置文件中的数据库密码
cp .env_example .env
vim .env

# 利用 docker-compose.yml 一件布置 PHP 和 MySQL 环境
mkdir -p certs/ certs-data/ logs/nginx/ mysql/ wordpress/
docker-compose up -d
docker-compose down

# 配置 SSL 使用到的 conf 文件
cd nginx
cp default.conf default.conf.template
cp default_https.conf.template default.conf
vim default.conf
把全部 FQDN_OR_IP 换位自己的域名

# Let's Encrypt 对应目录下
./letsencrypt-init.sh

# 对应目录下
docker-compose up -d
```

## 主题更换和插件安装
* 主题和插件都是下载后利用 SFTP 软件上传到服务器 WordPress 下面的相应 themes 和 plugins 文件夹下
* 推荐 [Kratos 主题](https://github.com/Vtrois/Kratos "Kratos 主题")
* Markdown 插件必装 [wp-editormd](https://wordpress.org/plugins/wp-editormd/ "wp-editormd")
* Markdown 显示目录插件 [table-of-contents-plus](https://wordpress.org/plugins/table-of-contents-plus/#description "table-of-contents-plus")

## Enjoy it!



