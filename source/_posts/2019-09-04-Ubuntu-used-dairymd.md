---
title: Ubuntu折腾日记
date: 2019-09-04 10:40:36
categories: 
tags: [Ubuntu,Linux]
---

# 锐捷便捷启动

* 准备好锐捷的Linux客户端，并确认自己的系统网卡型号，我的是eno1（如果**ifconfig**用不了，使用**ip a**命令）
* 新建一个rj_login.sh脚本文件到客户端文件夹所在位置，开机执行脚本**sudo sh rj_login.sh**

```sh
cd rjsupplicant
./rjsupplicant.sh -u 账号 -p 密码 -d 1 -n eno1 &
```
<!-- more -->
# 新系统首先要做的事情

* 如果有发现自己的**屏幕分辨率**不对劲（有黑边），一般不是驱动出了问题。在设置里面找找分辨率换一下，如果可选择的分辨率很少，可以参考 [这篇文章](https://blog.csdn.net/ignoreyou/article/details/79488442) 设置好自己电脑最适合的分别率。
* 安装好**Chrome**、**搜狗输入法**和**Vim**，我主要是参考这篇 [18.04](https://blog.csdn.net/haeasringnar/article/details/81809040)也可以参考这篇 [16.04](https://blog.csdn.net/skange/article/details/81127575)，关于**vim**的配置主要参考第二篇

* Chrome需要同步，需要科学上网，请参考[方法2](https://github.com/Turing2333/Detailed-tutorial-on-the-building-and-usage-of-SSR/blob/master/Instructions/Clients%20manual%20for%20each%20platform/Linux%20SSR%E7%9B%B8%E5%85%B3%E8%AF%B4%E6%98%8E.txt)。关于账号获取，在github搜索**new pac**也许你会有新发现...

* 上述一切办妥，也开启了ssr服务，可能还是上不了Google。此时需要在Chrome里设置 > 高级设置 > 系统 > 打开代理设置（Ubuntu会自动打开系统的 NetworkManager ），手动设置Chrome走ssr全局流量。这个时候有可能你会发现系统的NetworkManager失效。此时按一下步骤处理：
  ```sh
  # 首先看networkmanager有没有安装
  dpkg -l | grep networkmanager 
  # 如果没有输出，就说明没有装。安装networkmanager
  sudo apt install networkmanager 
  # 如果有输出，则已安装，有可能是服务没启动， 启动服务 
  sudo systemctl enable NetowrkManager 
  sudo systemctl start NetowrkManager 
  # 还不行的话，还有可能是网卡关闭了。打开就是了
  ```
	解决了上述问题，进入NetworkManager。选择手动(Manual)，在 Socks Host 输入 127.0.0.1  1080，之后便可以同步Chrome账户。之后为了在Chrome中进入国内网站使用直连，进入国外网站使用代理。在NetworkManager中选择自动模式，地址填入 https://raw.githubusercontent.com/petronny/gfwlist2pac/master/gfwlist.pac 并且在Chrome中使用 SwitchOmega 插件使用系统代理模式。

* 使用 **zsh** 和 **oh my zsh** 使得自己的终端好看又方便，参考[Oh-My-Zsh](https://github.com/robbyrussell/oh-my-zsh) 和 [插件](https://segmentfault.com/a/1190000018093021)。
  ```sh
  # 安装zsh 和 oh my zsh(通过curl)
  sudo apt-get install zsh
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

  # 安装插件
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

  # 配置用户主题和插件选项
  vim ~/.zshrc
  # 更改主题。ZSH_THEME="robbyrussell" 改为 ZSH_THEME="ys"
  # 更改插件设置,x命令解压一切文件，z命令快速跳转目录，google直接搜索内容。
  # plugins=(git) 改为 plugins=(git z extract web-search zsh-syntax-highlighting zsh-autosuggestions)

  # 更新设置
  source ~/.zshrc

  # 把bash默认设置为zsh
  chsh -s /bin/zsh （重启生效）
  ```

# 开始干活
