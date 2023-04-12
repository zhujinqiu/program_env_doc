# 导语

2023-4-11

对于机器学习er配置环境一直是个头疼的事，尤其是在windows系统中。尤其像博主这样的懒人，又不喜欢创建虚拟环境，过段时间又忘了环境和包的人，经常会让自己电脑里装了各种深度学习环境和python包。长时间会导致自己的项目文件和环境弄的很乱。且各个项目间的兼容性又会出现问题。

不仅如此，windows系统独特的“尿性”真的让开发者苦不堪言！

好在微软爸爸推出了WSL，WSL可以实现在windows电脑上运行linux系统。目前已经是越来越接近原生linux系统。

利用wsl 部署深度学习训练环境，无论是从**便捷性**上还是**性能上**均有优势。



博主浏览**目前wsl配置深度学习环境的各种文章，采坑无数**，终于完成了最详细的教程。

目前现在很多网上的**教程都是老版本**的**，真的有很多坑**！

由于现在很多环境和软件的升级，很多老教程不必要的操作就能直接省去！

这个教程就一个字：**新**！相比以往的老教程，无需走很多弯路!下面开始吧！



**==需要在WSL上玩深度学习，需要以下几个条件==**

- win11，最好更新到最新版本
- 电脑上有显卡，Nvdia
- windows上安装显卡驱动及CUDA和CuDNN（第一章）

- 安装WSL2 （2版本更好）

-  WLS2安装好Ubuntu20.04（本人之前试过22.04，有些版本不兼容的问题，无法跑通，时间多的同学可以尝试）（第二章）

**==在做好准备工作后，本文将介绍两种方法在WSL部署深度学习环境==**

- Docker法（**光速部署，不需要在WSL内安装任何驱动，方便迁移**）（第三章）
- wls2本地法（**在WSL2内直接安装CUDA**）（第四章）

# 1、windows显卡环境及CUDA安装

- 安装显卡驱动

  截止2023年,4月,全系显卡驱动已经指出

  [GPU in Windows Subsystem for Linux (WSL) | NVIDIA Developer](https://developer.nvidia.com/cuda/wsl)

  查看显卡驱动版本，及最大支持cuda版本，例如，如下图，本人最大支持12.1cuda

```bash
nvidia-smi
```

![image-20230410221740441](https://img.betazhu.top/blog/essayimage-20230410221740441.png)

- 安装合适的cuda版本和Cudnn

  [CUDA Toolkit Archive | NVIDIA Developer](https://developer.nvidia.com/cuda-toolkit-archive)

​	  [cuDNN Download | NVIDIA Developer](https://developer.nvidia.com/rdp/cudnn-download)

安装教程网上很多，推荐

[CUDA与cuDNN安装教程（超详细）_kylinmin的博客-CSDN博客](https://blog.csdn.net/anmin8888/article/details/127910084)

# 2、WSL 安装Ubuntu

## 2.1 wsl安装

如果你的windows为11版本，那么安装wsl会非常方便，这里教程可以参考微软官方wsl。wsl有两个版本，wsl1和wsl2，具体安装过程见下。一般我们默认使用wsl2版本。

[安装 WSL | Microsoft Learn](https://learn.microsoft.com/zh-cn/windows/wsl/install)

wsl的安装这里不介绍了，图太多。百度教程很多，这里就主要介绍下WSL安装Ubuntu。

以下步骤均在WSL2安装好的背景下镜像。

## 2.2 安装Ubuntu

查看发行版本，

```bash
wsl.exe --list --online
```

如果遇到以下错误

无法从“https://raw.githubusercontent.com/microsoft/WSL/master/distributions/DistributionInfo.json”中提取列表分发。无法解析服务器的名称或地址

![image-20230411134353638](https://img.betazhu.top/blog/essayimage-20230411134353638.png)

- 修改dns服务器

`网络和internet设置`-`高级网络设置`-``-``-`更多适配器选项`，选择你连接的网络，右键属性，将ipv4 DNS设置为`8.8.8.8`。

然后即可显示分装版本。

![image-20230411134955994](https://img.betazhu.top/blog/essayimage-20230411134955994.png)

![image-20230411135038406](https://img.betazhu.top/blog/essayimage-20230411135038406.png)





**安装合适版本**

建议选择20.04。22.04版本我试过，有不少坑点。

```bash
wsl --install -d Ubuntu-20.04
```

## 2.3 迁移至非系统盘

这一步主要是为了减少C盘的占用空间，默认WSL装 的linux子系统在C盘

docker-data默认安装在c盘，且设置中难以更改，因此采用如下操作。

- 1、shutdown 子系统

```bash
wsl --shutdown
```

- 2、导出Ubuntu


```bash
wsl --export Ubuntu-20.04 F:\Ubuntu\ubuntu.tar
```

- 3、注销docker-desktop和docker-desktop-data

```bash
wsl --unregister Ubuntu-20.04
```

- 4、导入

```
wsl --import Ubuntu-20.04 F:\Ubuntu\ F:\Ubuntu\ubuntu.tar --version 2
```

- 5、删除压缩包

```
del D:\docker-desktop-data\docker-desktop-data.tar
del D:\docker-desktop-data\docker-desktop-data.tar
```

重新启动docker

## 2.4 更改镜像源

这一步主要是为了加速linux各种包的下载速度。

首先做一个备份，这里就把Ubuntu默认安装的镜像源备份，接着更改源

```text
sudo cp  /etc/apt/sources.list /etc/apt/sources.list.bak
sudo vim /etc/apt/sources.list
```

##进入vim `ggdG`清空所有文本，复制清华源进vim编辑器中

打开如下源进行更新。注意Ubuntu版本选择20.04

[ubuntu | 镜像站使用帮助 | 清华大学开源软件镜像站 | Tsinghua Open Source Mirror](https://mirror.tuna.tsinghua.edu.cn/help/ubuntu/)

![image-20230411152733630](https://img.betazhu.top/blog/essayimage-20230411152733630.png)

更新一下

```
sudo apt-get update
sudo apt-get upgrade
```

**一般情况下：**

在win11版本wsl和最新版（2023.4)的NVIDIA驱动场景下，在WSL2内，通过` nvidia-smi`已经可以穿透显示显卡信息。![image-20230411135624544](https://img.betazhu.top/blog/essayimage-20230411135624544.png)


# 3、wls-Docker运行深度学习环境

适用Linux Docker配置深度学习环境。该方法使用Docker直接部署深度学习环境，方便快捷,并且不影响子系统本地。



Docker的部署方式分为两种，一种是使用desktop。即windows下载软件去部署docker；一种是使用linux版本的docker-engine。这就有点类似，一个使用图形化界面，一个使用小黑盒子。

这种desktop部署方式有优缺点：

优点：部署方便，可视化，适合小白

缺点：**极大地占用宿主（windows）的空间！**

- 1、Docker Data文件为**ext4.vhdx**动态磁盘形式，该磁盘特点是，占用容量只会增大，即使删除其内部镜像文件，占用空间也不会减少。

- 2、**即使使用3.2节方法，虽然docker-data可以迁移至别的盘，但对镜像进行操作（如push），windows会产生临时文件夹，同样会占用C盘空间！**

  留下贫穷的泪水!o(╥﹏╥)o

- 3、性能问题，docker-desktop目前还有些bug未修复。



**对于不喜欢折腾的选3.1即可**。

**3.1与3.2方式二选一即可，但博主建议使用3.2节linux 原生docker-Engine进行配置！！！！**

**3.1与3.2方式二选一即可，但博主建议使用3.2节linux 原生docker-Engine进行配置！！！！**

**3.1与3.2方式二选一即可，但博主建议使用3.2节linux 原生docker-Engine进行配置！！！！**

## 3.1 docker desktop 深度学习环境配置

### 3.1.1 docker desktop安装

[Install Docker Desktop on Windows](https://docs.docker.com/desktop/install/windows-install/)

- 在windows上安装docker-Desktop

  别选4.17.1版本！！！！该版本无法调用gpus all

  **目前4.18.0已结修复该bug**

- 设置

  **1、镜像源**

  将如下代码加入Docker Engine中

  ![image-20230410141832996](https://img.betazhu.top/blog/essayimage-20230410141832996.png)


```bash
"registry-mirrors": [
      "https://registry.docker-cn.com",
      "http://hub-mirror.c.163.com",
      "https://docker.mirrors.ustc.edu.cn"
  ],
```

**2- 系统设置**

打开两个按钮

`Settings`---->`General`--->`Use the WSL 2 based engine`



​	 `Settings`---->`Resources`--->`Enable integration with additional distros`

![image-20230410142215781](https://img.betazhu.top/blog/essayimage-20230410142215781.png)

测试是否安装成功，在wsl Ubuntu系统下输入，

```bash
docker run hello-world
```



### 3.1.2 **wsl 切换docker数据存储位置**

docker-data默认安装在c盘，占用很大空间，且设置中难以更改，因此采用如下操作。

- 1、shutdown docker

```bash
wsl --shutdown
```

- 2、导出docker-desktop和docker-desktop-data


```bash
wsl --export docker-desktop-data F:\wsl2\docker-desktop-data.tar
wsl --export docker-desktop  F:\wsl2\docker-desktop.tar
```



- 3、注销docker-desktop和docker-desktop-data

```bash
wsl --unregister docker-desktop-data
wsl --unregister docker-desktop
```

- 4、导入

```bash
wsl --import docker-desktop-data F:\wsl2\docker-desktop-data F:\wsl2\docker-desktop-data.tar --version 
wsl --import docker-desktop F:\wsl2\docker-desktop F:\wsl2\docker-desktop.tar --version 2
```

- 5、删除压缩包

```bash
del F:\wsl2\docker-desktop-data.tar
del F:\wsl2\docker-desktop-data.tar
```

重新启动docker

### 3.1.3 调用测试docker gpu

dock 官网给的条件已结满足，由于目前版本已结最新,可直接调用，无需安装其他内容。

Starting with Docker Desktop 3.1.0, Docker Desktop supports WSL 2 GPU Paravirtualization (GPU-PV) on NVIDIA GPUs. To enable WSL 2 GPU Paravirtualization, you need:

- A machine with an NVIDIA GPU（有英伟达显卡）
- The latest Windows Insider version from the Dev Preview ring（windows版本更细）
- [Beta drivers](https://developer.nvidia.com/cuda/wsl) from NVIDIA supporting WSL 2 GPU Paravirtualization（最新显卡驱动即可）
- Update WSL 2 Linux kernel to the latest version using `wsl --update` from an elevated command prompt（最新WSL版本）
- Make sure the WSL 2 backend is enabled in Docker Desktop （Docker中设置WSL 2 backend勾选上）

运行如下代码测试

```bash
docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
```

如果得到,则说明安装成功。

> Compute 7.5 CUDA device: [NVIDIA GeForce RTX 2070 SUPER]
> 40960 bodies, total time for 10 iterations: 56.638 ms
> = 296.218 billion interactions per second
> = 5924.356 single-precision GFLOP/s at 20 flops per interaction

## 3.2 wsl-linux原生docker-engine深度学习环境

**docker engine装在wsl内部，相比于docker-desktop，能极大节省windows宿主空间**！！！！

且运行效率更高

### 3.2.1 linux-docker-engine 安装

wsl原生docker安装方式和docker doc安装方法一致

[Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/)

**首先卸载老版本**

```bash
sudo apt-get remove docker docker-engine docker.io containerd runc
```

**建立储存库**

```bash
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg
```

**添加官方的gpgkey**

```bash
sudo mkdir -m 0755 -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

**设置版本库**

```bash
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

**更新apt**

```bash
sudo apt-get update
```

**安装Docker Engine, containerd, and Docker Compose.**

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

**验证helloword**

```bash
sudo docker run hello-world
```

**配置镜像源**

和docker desktop一样配置镜像源，建议使用阿里云

登录->`控制台`->搜索`容器镜像服务`->'镜像加速器'

![](https://img.betazhu.top/blog/essayimage-20230412135544895.png)

通过**修改daemon配置文件/etc/docker/daemon.json来使用加速器**

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://你自己的.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```







**ps:在上述流程中如果碰到 ` Temporary failure resolving ‘gb.archive.ubuntu.com’`**

Temporary resolve问题，加入dns服务器

如果这能解决你的临时解析信息，那么要么等待24小时，看看你的ISP是否为你解决了问题（或者直接联系你的ISP）--或者你可以永久性地在你的系统中添加一个DNS服务器：

```
echo "nameserver 8.8.8.8" | sudo tee /etc/resolvconf/resolv.conf.d/base > /dev/null
```



### 3.2.2 wsl - docker-engine自启动 (实现systemctl )

在单独的linux系统中,systemctl 可以对docker进行自启动，但是wsl初始系统并不支持systemctl ，每次重新启动wsl，都需要`sudo service docker start`

**现在WSL2有了systemd的支持，我们可以在没有Docker桌面的情况下在WSL中运行Docker！！**

教程：

**step1 启动systemctl **

**启动wsl内部的linux分发版本**，运行如下代码

```bash
# Enable systemd via /etc/wsl.conf
{
cat <<EOT
[boot]
systemd=true
EOT
} | sudo tee /etc/wsl.conf

exit
```

或者编辑vim /etc/wsl.conf

使得

```
[boot]
systemd=true
```



ps:上述设置，` sudo systemctl start docker`有还有问题的小伙伴。，可以换成

```
[boot]
#systemd=true
command = sudo service docker start
```



**step2 重启，测试systemctl **

```bash
wsl --shutdown
wsl --distribution Ubuntu ##Ubuntu指你的镜像版本，按wsl -l -vs输出的第一列来
```

**测试过systemd 是否在运行**

```bash
systemctl --no-pager status user.slice > /dev/null 2>&1 && echo 'OK: Systemd is running' || echo 'FAIL: Systemd not running'
```

**step3 docker设置**

```bash
# Install docker-compose
sudo ln -s /usr/libexec/docker/cli-plugins/docker-compose /usr/bin/docker-compose
```

```bash
# 加入docker组
sudo usermod -aG docker $USER

#退出bash
exit
```

**重新登陆**

```
wsl --distribution Ubuntu
```

这时你会发现，不用`sudo service docker start`,docker也能启动了

而且，也能使用一些列如如下命令。

```bash
sudo systemctl restart docker
sudo systemctl start docker
```



### 3.2.3  NVIDIA Container Toolkit安装



nvidia-container-toolkit是使得linux系统在docker容器中具有调用gpu的能力。

![nvidia-gpu-docker](https://img.betazhu.top/blog/essay5b208976-b632-11e5-8406-38d379ec46aa.png)

使用条件:

[NVIDIA/nvidia-docker: Build and run Docker containers leveraging NVIDIA GPUs (github.com)](https://github.com/NVIDIA/nvidia-docker)

已经说明：

Make sure you have installed the [NVIDIA driver](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) and Docker engine for your Linux distribution.

Note that you do not need to install the CUDA Toolkit on the host system, but the NVIDIA driver needs to be installed.

**因此只用去x显卡装驱动和Docker就完事了！**

安装教程[Installation Guide — NVIDIA Cloud Native Technologies documentation](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html#docker)

建立package repository 和 GPG key:

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/experimental/$distribution/libnvidia-container.list | \
         sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
         sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```bash
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

设置Docker daemon 守护进程识别Nvidia容器Runtime

```
sudo nvidia-ctk runtime configure --runtime=docker
```

```
##重启一下docker
udo systemctl restart docker
```

### 3.2.4 调用测试gpu

```bash
docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
```

> Compute 7.5 CUDA device: [NVIDIA GeForce RTX 2070 SUPER]
> 40960 bodies, total time for 10 iterations: 56.638 ms
> = 296.218 billion interactions per second
> = 5924.356 single-precision GFLOP/s at 20 flops per interaction

则运行成功

## 3.3 自定义自己docker

无论是利用3.1Docker-desktop建立的还是Docker-engine建立的环境，均可以进行以下操作。

**出发点:**

考虑到目前官方docker-hub中提供的imag并不能满足日常的开发包的需要，且如果容器丢失或删除，所有包的数据和项目文件均会丢失。因此，我们可以定制自己的image。并将container的项目文件映射到自己的本地文件目录中。

可以使用vscode完成以下操作，方便编辑一点。

进入vscode，安装wsl 插件，连接wls（或着在wsl linux终端下，输入`code .`进入

ps: docker开发ide也可以使用vscode，只需要对安装Docker插件即可。

打开终端，如下图，当左下角绿色显式WSL:Ubutun...为连接成功

![image-20230411173949485](https://img.betazhu.top/blog/essayimage-20230411173949485.png)

创建一个以后存docker镜像的目录

```bash
mkdir docker-deeplearning-project
cd docker-deeplearning-project
```

### 3.3.1 自定义所需要安装的包

我们想要在 `tensorflow/tensorflow:latest-gpu	`的基础上增加一些别的包，以满足日常需求，可以使用如下方法。打开vscode的终端，创建额外的requirments.txt！

`code  requirments.txt`

**安装以下包，这些包可以随各位去配**

**torch那可以指定版本，在torch官网可以挑选**

```txt
jupyterlab
pandas
matplotlib
torch --index-url https://download.pytorch.org/whl/cu118
torchvision --index-url https://download.pytorch.org/whl/cu118
torchaudio --index-url https://download.pytorch.org/whlcu118
```



### **3.3.2 建立Dockerfile**

dockfile建立规则

![image-20230412124242109](https://img.betazhu.top/blog/essayimage-20230412124242109.png)



```bash
code Dockerfile
```

Dockerfile输入一下代码:

- FROM:拉取现有base image

- WORKDIR:定制工作目录
- COPY 拷贝本地目录的requirements.txt到当前dockerfile中，通过此去安装额外的requirments包，
- RUN 运行安装程序，-i 指定镜像源
- EXPOSE 开辟一定端口
- ENTRYPOINT 定义入口点，通过列表构建命令

```dockerfile
FROM tensorflow/tensorflow:latest-gpu

WORKDIR /tf-jinqiu

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple some-package

EXPOSE 8888

ENTRYPOINT ["jupyter","lab","--ip=0.0.0.0","--allow-root","--no-browser"] 
```

![image-20230411174331458](https://img.betazhu.top/blog/essayimage-20230411174331458.png)





### **3.3.3建立docker-compose.yaml**

![image-20230412124200428](https://img.betazhu.top/blog/essayimage-20230412124200428.png)

```bash
code docker-compose.yaml
```

解析：

- version：版本

- services：定义服务

- jupyter-lab：image名称

- build: . 在当前目录

- ports：映射端口

- volumes:保存访问文件的部分。将其映射到本地文件中
- deploy：share GPU

```yaml
version: '1.0'
services:
  jupyter-lab:
    build: .
    ports:
      - "8888:8888"
    volumes:
      - /tf-jinqiu:/tf-jinqiu
    deploy:
      resources:
        reservations:
          devices:
            - driver: nvidia
              count: 1
              capabilities: [gpu]
```

### 3.3.4 运行并测试

```bash
docker-compose up
```

出现如下输出，则证明安装成功。

![image-20230411183301232](https://img.betazhu.top/blog/essayimage-20230411183301232.png)

复制底下的：http://127.0.0.1:8888/lab?token=d6e406efda3edf086b2baf4296a9400d53bee0d4221e1b9e

即可jupyter -lab

测试pytorch和TensorFlow ,gpu版本均能运行！！！

**注意文件映射位置**：

因此我们项目地址可以存在container中`/tf-jinqiu`中。

开发项目的地址为`docker-compose.yaml`中设置的`/tf-jinqiu`。因此在container中`/tf-jinqiu`和wls ubuntu `/tf-jinqiu`目录下均有映射的文件。

![image-20230411183129497](https://img.betazhu.top/blog/essayimage-20230411183129497.png)



进入vscode docker中，在容器中右键 Attach Visual Studio Code即可进入容器内，进行相关project的开发。

![image-20230411183702094](https://img.betazhu.top/blog/essayimage-20230411183702094.png)



### 3.3.5 push 上传dockerhub

dockerhub可以上传官网的dockerhub，也可以上传阿里云的。

上传自己建好的docker可以方便以后迁移机器，直接部署。

**(1) 官方dockerhub**

保存自己docker的方式，可以采用docker-file配合docker-compose.yaml的方式，也可采用将整个镜像上传至Docker-hub中，一劳永逸。

保存dockerhub中，每次换机器只需要从docker中拉取镜像，即可快速部署相关环境。

- 登录docker

```bash
docker login -u zhujinqiu   # zhujinqiu为自己的账号
```

![image-20230411210745496](https://img.betazhu.top/blog/essayimage-20230411210745496.png)

- docker容器变成镜像

```bash
// 找到运行中的容器 (复制你要打包的容器的id)
docker ps
// 打包为镜像 （86d78d59b104：容器的id 、  my_centos：我们要打包成的镜像的名字）
docker commit zhujinqiu my_centos
// 找到打包的镜像
docker images
```

- 将镜像打为标签

```bash
// my_centos：镜像的名字   、 zhujinqiu：我们docker仓库的用户名 、 my_centos：我们刚才新建的仓库名 、 v1：版本号，可以不设置
docker tag my_centos zhujinqiu/my_centos:v1

```

- 将镜像上传到镜像仓库

```bash
docker push zhujinqiu/my_centos:v1
```

![image-20230411211626036](https://img.betazhu.top/blog/essayimage-20230411211626036.png)

- 下载自己的镜像

```bash
// 记得先登录
docker login
// 根据版本号拉取
docker pull zhujinqiu/my_centos:v1
```

- 修改镜像名字

```bash
// d583c3ac45fd  ID   后者是需要修改的名字
docker tag d583c3ac45fd myname:latest
```

**(2)阿里云镜像仓库**

[容器镜像服务 (aliyun.com) ](https://cr.console.aliyun.com/cn-hangzhou/instances)

阿里云有详细的

首先创建镜像仓库仓库创建指南。

![image-20230412142635611](https://img.betazhu.top/blog/essayimage-20230412142635611.png)

1. **登录阿里云Docker Registry**

```
$ docker login --username=你的用户名 registry.cn-hangzhou.aliyuncs.com
```

用于登录的用户名为阿里云账号全名，密码为开通服务时设置的密码。

您可以在访问凭证页面修改凭证密码。

2. **从Registry中拉取镜像**

```
$ docker pull registry.cn-hangzhou.aliyuncs.com/命名空间/仓库名称:[镜像版本号]
```

3. **将镜像推送到Registry**

```
$ docker login --username=你的用户名 registry.cn-hangzhou.aliyuncs.com
$ docker tag [ImageId] registry.cn-hangzhou.aliyuncs.com/命名空间/仓库名称:[镜像版本号]
$ docker push registry.cn-hangzhou.aliyuncs.com/命名空间/仓库名称:[镜像版本号]
```

请根据实际镜像信息替换示例中的[ImageId]和[镜像版本号]参数。

4. **选择合适的镜像仓库地址**

从ECS推送镜像时，可以选择使用镜像仓库内网地址。推送速度将得到提升并且将不会损耗您的公网流量。

如果您使用的机器位于VPC网络，请使用 registry-vpc.cn-hangzhou.aliyuncs.com 作为Registry的域名登录。

 5. **示例**

使用"docker tag"命令重命名镜像，并将它通过专有网络地址推送至Registry。

```
$ docker imagesREPOSITORY                                                         TAG                 IMAGE ID            CREATED             VIRTUAL SIZEregistry.aliyuncs.com/acs/agent                                    0.7-dfb6816         37bb9c63c8b2        7 days ago          37.89 MB$ docker tag 37bb9c63c8b2 registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```

使用 "docker push" 命令将该镜像推送至远程。

```
$ docker push registry-vpc.cn-hangzhou.aliyuncs.com/acs/agent:0.7-dfb6816
```



# 4、wsl本地运行深度学习环境

该方法不借助Docker，因此需要直接在本地配置好WSL-Cuda，并安装各类深度学习框架，类似于把Windows上跑的模型搬到WSL跑。

该方法只需要安装Cuda。并不要装显卡驱动和Cudnn（wsl2已经支持本地Windows映射）。

## 4.1 安装wsl Cuda

[CUDA Toolkit 12.1 Downloads | NVIDIA Developer](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_local)

![image-20230409233638445](https://img.betazhu.top/blog/essayimage-20230409233638445.png)

**根据驱动情况、pytorch、Tensorflow情况选择合适的wsl——cuda版本。需要更改如下适应“12-1为11-8”**

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-11-8-local_11.8.0-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda
```

安装完需要，**设置环境变量**

```bash
vim ~/.bashrc
```

在最后加入，配置环境变量

```bash
export PATH=/usr/bin:$PATH
export PATH=/usr/local/cuda-11.8/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64:$LD_LIBRARY_PATH
```

更新下source

```bash
source ~/.bashrc
```

使用nvcc -V验证安装成功与否

```bash
nvcc -V
```

![image-20230409234425587](https://img.betazhu.top/blog/essayimage-20230409234425587.png)



## 4.2 安装pytorch环境

1、miniconda安装

```bash
# 下载安装包
wget -c https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
chmod 777 Miniconda3-latest-Linux-x86_64.sh
# 安装conda
bash Miniconda3-latest-Linux-x86_64.sh

```

安装完需要，**设置环境变量**

```bash
vim ~/.bashrc
```



更新下source

```bash
source ~/.bashrc
```



 配置miniconda环境变量

```bash
export PATH=~/miniconda3/bin:$PATH
```

## 4.3 创建虚拟环境与pytorch安装、tensorflow安装

```bash
conda create -n env_pytorch python=3.8

# conda env list 查看虚拟环境list
# 激活
conda activate env_pytorch
```

1、安装pytorch

[Start Locally | PyTorch](https://pytorch.org/get-started/locally/)

根据cuda版本选择合适的torchgpu版本

```bash
## -i 清华镜像加速 安装torchgpu
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118 -i https://pypi.tuna.tsinghua.edu.cn/simple some-package
```

2、安装tensorflow

ps:新版本直接安装即可

[TensorFlow (google.cn)](https://tensorflow.google.cn/install?hl=zh-cn)

```bsh
# Requires the latest pip
pip install --upgrade pip

# Current stable release for CPU and GPU
pip install tensorflow

```

## 4.4 测试gpu

```python
## torch
import torch
torch.cuda.is_available()
## tensorflow
import tensorflow as tf
tf.config.list_physical_devices()
```

