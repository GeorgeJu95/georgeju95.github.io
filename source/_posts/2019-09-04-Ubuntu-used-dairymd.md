---
title: Ubuntu折腾日记
date: 2019-09-04 10:40:36
categories: 
tags: [Ubuntu,Linux]
---

**目前尽量选择 Ubuntu 16.04 而不是 Ubuntu 18.04, 安装18.04对于安装OpenCV等库非常容易出问题**
**如果是 AMD驱动 并且为了使用kinect做开发，最好使用 Ubuntu 14**，原因后面会提到

# 锐捷便捷启动

* 准备好锐捷的Linux客户端，并确认自己的系统网卡型号，我的是eth0（如果**ifconfig**用不了，使用**ip a**或者**sudo lshw -C network**命令）
* 新建一个rj_login.sh脚本文件到客户端文件夹所在位置，开机执行脚本**sudo sh rj_login.sh**

```sh
cd rjsupplicant
./rjsupplicant.sh -u 账号 -p 密码 -d 1 -n eth0 &
```
<!-- more -->
# 新系统首先要做的事情

* Ubuntu16 自带的python 2.7和3.5没有pyvenv等包，最好是安装稳定的python最新版，并且设置python3指向python 3.7 参考[Python3.7.5](https://segmentfault.com/a/1190000018264955?utm_source=tag-newest)
* 如果有发现自己的**屏幕分辨率**不对劲（有黑边），一般不是驱动出了问题。在设置里面找找分辨率换一下，如果可选择的分辨率很少，可以参考 [这篇文章](https://blog.csdn.net/ignoreyou/article/details/79488442) 设置好自己电脑最适合的分别率。
* 安装好**Chrome**、**搜狗输入法**和**Vim**，我主要是参考这篇 [18.04](https://blog.csdn.net/haeasringnar/article/details/81809040)也可以参考这篇 [16.04](https://blog.csdn.net/skange/article/details/81127575)，关于**vim**的配置主要参考第二篇。**护眼产品**（包括f.lux、redshift和okular）参考 [redshift和okular](https://blog.csdn.net/u011092188/article/details/59169205) 和 [f.lux](https://blog.csdn.net/gatieme/article/details/62922164)。**下载工具**参考 [uget](https://cnblogs.com/reaptomorrow-flydream/p/9526454.html)。

* Chrome需要同步，需要科学上网，请参考[方法2](https://github.com/Turing2333/Detailed-tutorial-on-the-building-and-usage-of-SSR/blob/master/Instructions/Clients%20manual%20for%20each%20platform/Linux%20SSR%E7%9B%B8%E5%85%B3%E8%AF%B4%E6%98%8E.txt)。如果执行sudo npm install -g ssr-helper时报GERT_UNTRUSTED错误，这是因为ssl出了问题，执行**npm config set strict-ssl false**。此时的node版本可能较低，安装 **n** 升级node，分别执行 **sudo npm install n -g** 和 **sudo n stable**。关于账号获取，在github搜索**new pac**也许你会有新发现...

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
  # 用以下命令临时打开NetworkManager
  service NetworkManager start
  ```
	解决了上述问题，进入NetworkManager。选择手动(Manual)，在 Socks Host 输入 127.0.0.1  1080，之后便可以同步Chrome账户。之后为了在Chrome中进入国内网站使用直连，进入国外网站使用代理。在NetworkManager中选择自动模式，地址填入 https://raw.githubusercontent.com/petronny/gfwlist2pac/master/gfwlist.pac 并且在Chrome中使用 SwitchOmega 插件使用系统代理模式。

* 使用 **zsh** 和 **oh my zsh** 使得自己的终端好看又方便，参考[Oh-My-Zsh](https://github.com/robbyrussell/oh-my-zsh) 和 [插件](https://segmentfault.com/a/1190000018093021)。
  ```sh
  # 安装zsh 和 oh my zsh(通过curl)
  sudo apt-get install zsh
  sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

  # 安装插件
  git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
  git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

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

* 博客搬到新电脑
  ```sh
  # 克隆仓库到本地
  git clone https://github.com/GeorgeJu95/georgeju95.github.io
  # 安装 hexo 并初始化
  sudo npm install -g hexo-cli
  sudo npm install –no-bin-links  (这一步一定要在项目目录下)
  sudo npm install hexo-deployer-git
  ```

* LeetCode项目搬运到新电脑
  ```sh
  # 安装chrome 和 chromedriver
  # https://chromedriver.storage.googleapis.com/index.html 下载对应chrome版本的chromedriver，之后依次执行
  
  git clone https://github.com/bonfy/leetcode
  sudo apt-get install xvfb
  sudo chmod +x chromedriver
  sudo mv -f chromedriver /usr/local/share/chromedriver
  sudo ln -s /usr/local/share/chromedriver /usr/local/bin/chromedriver
  sudo ln -s /usr/local/share/chromedriver /usr/bin/chromedriver

  # 之后 参考 https://github.com/bonfy/leetcode 说明文档里的步骤
  sudo apt-get install python3-pip   (如果pip3没有安装的话)
  # 升级 pip3
  pip3 install --upgrade pip 
  # 有可能升级 pip 后出现问题，参考下面文章方法2 
  # https://blog.csdn.net/zong596568821xp/article/details/80410416
  sudo pip3 install requests selenium pyquery
  # 使用 python 虚拟环境 https://blog.csdn.net/qq455013140/article/details/79413414
  sudo pip3 install virtualenv 
  # 在当前目录生成venv文件夹
  virtualenv venv
  # 激活虚拟环境
  source venv/bin/activate
  pip3 install -r req.txt
  # 退出虚拟环境
  deactivate
  ```

# 开始干活

## 安装Ubuntu 16.04 + kinect v2

* 首先安装 [libfreenect2](https://github.com/OpenKinect/libfreenect2#debianubuntu-1404)

  * Install OpenCL (optional) 这里要注意，虽然是可选的，但是**建议安装上**，不然后面用 ./bin/Protonet/cl 模式跑示例程序会出问题，而且 iai_kinect2 也是默认用的 opencl 做 registration。因为要更新到最新的驱动，稍微有点麻烦（对于AMD显卡）【已经放弃，转投Windows】
    * 首先查看自己的显卡驱动  lspci |grep VGA 
    * 如果是AMD显卡，需要安装最新的驱动，可以去[官网](https://www.amd.com/en/support/kb/release-notes/rn-prorad-lin-amdgpupro-17-40)下载 **AMDGPU-pro**（我安装了重启总是有问题，按**Ctrl+Alt+F1**进入命令行模式，cd进入驱动所在文件夹，**./amdgpu-pro-install —px**可破，然后卸载驱动...）；也可以在ubuntu系统更新驱动（我总是显示我的驱动已经最新…）。
    * 最后发现 Ubuntu 对 AMD驱动的支持问题由来已久，请参考 [AMD显卡](https://ubuntuqa.com/article/1602.html)。因此选择折腾14系列。。。
  * `Ubuntu14.04.6` 14.04.5带来了Xorg 1.18，因此我们需要先降级，再安装 **fglrx**。过程中主要参考了这几篇文章，[1](https://blog.csdn.net/qq_35843543/article/details/82048193)、[2](https://www.cnblogs.com/qiaoyanlin/p/6901177.html)、[3](https://blog.csdn.net/u010467276/article/details/84867836)。

  ```bash
  dpkg -l | grep "ii  xserver-xorg-core"    # 查看xorg版本，1.18是不能安装fglrx的

  sudo apt-get install xserver-xorg-lts-utopic libqt5gui5 libgles1-mesa-lts-utopic libgles2-mesa-lts-utopic libgl1-mesa-glx-lts-utopic libgl1-mesa-glx-lts-utopic:i386 libglapi-mesa-lts-utopic:i386 libegl1-mesa-drivers-lts-utopic    # 降级到1.16

  sudo lsb_release -a.   # 查看发行版本
  sudo uname -m       # 查看架构
  lspci |grep VGA     # 查看显卡驱动

  * 去这里找到并下载对应的AMD显卡驱动
  http://support.amd.com/en-us/download

  * 导出 fglrx 四个安装包
  x 你下的版本.zip    # 注意 x 命令是只有安装了上文提到的 zsh 和 oh my zsh才有的解压命令

  cd 到.run文件所在的文件夹

  sudo aptitude install build-essential cdbs dh-make dkms execstack dh-modaliases  libqtgui4 debhelper debconf libstdc++6 dkms libqtgui4 libelfg0 linux-headers-generic   # 安装依赖
  sudo aptitude install lib32gcc1     # 64位版本还需要这个命令

  sudo sh ./xxx.run    #  这个命令后面会生成四个 fglrx deb包，提示是否安装，选择否，我们手动安装（自动安装没有测试）
  再就是参考上面提到的文章，进行四个包的手动安装
  然而然并卵，照样安装后无限登陆。于是真心放弃Ubuntu了。。。没办法。AMD 显卡傻逼
  ```

  * 最后的 cmake 可以添加参数 -DENABLE_CXX11=ON 这样子会**支持C++11**，默认是关闭的，即输入**cmake .. -DENABLE_CXX11=ON -DCMAKE_INSTALL_PREFIX=$HOME/freenect2**

* 再安装 [ROS](http://wiki.ros.org/kinetic/Installation/Ubuntu) 注意最后建立环境如果你用的是 **zsh** 而不是 bash 有区别对待，这也是**官方文档的好处**，CSDN上一票教程都没有提到这点。可以跑一个测试程序——小海龟，分别在三个终端中输入后三条命令：

  ```bash
  sudo apt-get install ros-kinectic-turtlesim   # 安装事例，一般已经安装
  roscore   # 如果报not configured properly错误，在~/.zshrc中添加两行 1、export ROS_HOSTNAME=localhost 2、export ROS_MASTER_URI=http://localhost:11311
  rosrun turtlesim turtlesim_node
  rosrun turtlesim turtle_teleop_key
  ```

* 安装OpenCV 2.XX [参考opencv3.x安装](https://blog.csdn.net/qq_18649781/article/details/85927212) 和 [PCL](https://blog.csdn.net/Bluenapa/article/details/84026874)、eigen等

  ```bash
  sudo apt-get install libeigen3-dev  # 安装eigen
  
  sudo apt-get install libpcl-dev pcl-tools  # 安装pcl，还需要一点其他的工作，参考上面文章
  
  sudo apt-get install build-essential libgtk2.0-dev libvtk5-dev libjpeg-dev libtiff5-dev libjasper-dev libopenexr-dev libtbb-dev  # opencv的一些依赖项
  mkdir build && cd build   # 在opencv文件夹下
  cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
  make -j4
  sudo make install
  sudo /bin/bash -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'   # 配置环境
  sudo ldconfig
  sudo vim /etc/profile     # 指定头文件位置
  #添加export  PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
  ```

* 安装 [iai_kinect2](https://github.com/code-iai/iai_kinect2) 

* 最后写~~(抄)~~一个获取数据的小程序啰，参考 [1](https://blog.csdn.net/xiaocainiaodeboke/article/details/52353191)、[2](https://blog.csdn.net/u012424737/article/details/80609451)、[3](https://blog.csdn.net/weixin_34375233/article/details/89663653)。

* <mark>在尝试了安装amdgpu-pro-18.40版本后，出现了转机，系统不用重装，小程序也跑的动，但是问题还是出在openCL模式，出现整个屏幕变黑的状况。。。。于是正式放弃Ubuntu，以后买显卡注意不买 AMD</mark>

   