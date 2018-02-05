# 一、版本说明

2017年2月份，Docker公司发布了全新的Docker版本：V1.13.0。从2017年3月1号开始，Docker的版本命名发生如下变化：

| **项目** | **说明** |
| :--- | :--- |
| 版本格式 | YY.MM |
| stable版本 | 每个季度发行 |
| edge版本 | 每个月发行 |

同时将Docker分成CE和EE 2个版本。CE版本即社区版（免费，支持周期三个月），EE即企业版，强调安全，付费使用。

Docker会每月发布一个edge版本\(17.03, 17.04, 17.05...\)，每三个月发布一个stable版本\(17.03, 17.06, 17.09...\)，企业版\(EE\)和stable版本号保持一致，但每个版本提供一年维护。

Docker的Linux发行版的软件仓库也从以前的[https://apt.dockerproject.org](https://apt.dockerproject.org/)和[https://yum.dockerproject.org](https://yum.dockerproject.org/)变更为目前的[https://download.docker.com](https://download.docker.com/)。软件包名变更为docker-ce\(社区版\)和docker-ee\(企业版\)。

当前的CE版本为17.03.0，基于V1.13.0。主要修复错误，没有重大功能增加，API亦保持不变。本文以此版本安装。

此版本的发行说明，请参考：https://github.com/docker/docker/releases



# 二、安装Docker

1. 升级安装

 1.1卸载安装的所有Docker组件

 1.1.1卸载插件

在Docker17.03.0-ce版本中，与在Docker 1.12中引入的实验版本相比，管理插件API发生了变化。在升级到Docker17.03.0-ce之前，必须卸载使用Docker 1.12安装的插件。可通过 docker plugin rm 命令卸载插件。

要手动删除所有插件并解决此问题，请执行以下步骤：

* 从/var/lib/docker/plugins/中删除plugins.json

* 重新启动Docker，验证Docker守护程序启动时是否有错误。

* 重新安装插件

* 卸载Docker安装包

```commandline
yum remove docker\*
```

 1.1.2卸载相关组件container-selinux（必须卸载，不然会报冲突的错误）

```commandline
yum remove  container-selinux-1.12.5-14.el7.centos.x86\_64
```


* 下载官方Docker YUM源

登陆Docker官网[https://www.docker.com/](https://www.docker.com/)。首页点击Get Docker Community Edition

在下载页面选择对应的操作系统版本。然后在按照页面说明和操作步骤安装Docker CE

* 安装docker CE

* 安装yum-utils

```commandline
sudo yum install -y yum-utils 
```


1.下载docker yum源

编辑.repo文件
```commandline
vi /etc/yum.repos.d/docker.repo
```
内容
```commandline
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/7/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
```

2.更新软件缓存

```commandline
yum update
```


3.获取、安装Docker CE

```commandline
yum -y install docker-engine
```


4.启动docker并查看安装的docker版本

```commandline
systemctl start docker
```


5.查看版本信息

```commandline
docker info
``` 


2.全新安装

全新安装步骤不需要删除已安装的Docker和插件，直接配置官方YUM源安装即可。


# 三、使用Docker

* 配置镜像加速器

国内访问Docker Hub有时会遇到困难，此时可以配置镜像加速器。国内很多云服务商都提供了加速器服务，例如：

* 阿里云加速器

* DaoCloud加速器

* 灵雀云加速器

注册用户并且申请加速器，会获得如https://jxus37ad.mirror.aliyuncs.com这样的地址。我们需要将其配置到Docker引擎。



下面以CentOS7为例，说明如何配置镜像加速器

1. 设置Docker开机启动

```commandline
systemctl enable docker
```


1. 修改docker.service配置文件

```commandline
vi /etc/systemd/system/multi-user.target.wants/docker.service
```

找到ExecStart=这一行，在这行最后添加加速器地址--registry-mirror=&lt;加速器地址&gt;，如：ExecStart=/usr/bin/dockerd--registry-mirror=https://jxus37ad.mirror.aliyuncs.com 

添加Spring Boot 支持：

-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock


重新加载配置

```commandline
sudo systemctl daemon-reload
```

重新启动Docker

```commandline
sudo systemctl restart docker
```


1. 验证是否生效

执行如下命令:

```commandline
ps -ef  \| grep dockerd
```

如果从结果中看到了配置的--registry-mirror参数说明配置成功。
  


* 启动Docker，获取镜像

执行如下命令启动Docker

```commandline
systemctl start docker
```

Docker运行容器前需要本地存在对应的镜像，如果镜像不存在本地，Docker会从镜像仓库下载（默认是Docker Hub公共注册服务器中的仓库）。

例如从镜像仓库下载ubuntu16.04镜像

```commandline
docker pull ubuntu:16.04
```

* 运行容器

有了镜像后，我们就可以以这个镜像为基础启动一个容器来运行。以上面的ubuntu:16.04为例，如果我们打算启动里面的bash并且进行交互式操作的话，可以执行下面的命令。

```
docker run -it  ubuntu:16.04  /bin/bash
```


* 查看系统版本

```
cat /etc/os-release
```




