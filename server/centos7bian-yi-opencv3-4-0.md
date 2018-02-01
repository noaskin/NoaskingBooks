# CentOS7编译OpenCV3.4.0

## 一、配置epel源 {#一配置epel源}

```
yum -y install epel-release
```

## 二、安装依赖包和基础包 {#二安装依赖包和基础包}

制作一个bash文件，一键运行。新建文件：

```
vim install-ffmpeg.sh
```

内容如下：

```
yum install -y vim 
yum install -y epel-release 
sudo rpm –import /etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-7 
yum repolist 
sudo rpm –import http://li.nux.ro/download/nux/RPM-GPG-KEY-nux.ro 
sudo rpm -Uvh http://li.nux.ro/download/nux/dextop/el7/x86_64/nux-dextop-release-0-1.el7.nux.noarch.rpm 
yum repolist 
yum update -y 
yum install -y ffmpeg 
ffmpeg -version
```

设定文件可执行：

```
chmod +x install-ffmpeg.sh
```

执行

```
./install-ffmpeg.sh
```

最后安装

```
yum install ffmpeg-devel
```

## 四、安装ant {#四安装ant}

安装ant，使得opencv编译java接口包。

```
yum -y install ant
```

## 五、编译opencv {#五编译opencv}

下载源码解压，根目录下新建文件夹

```
mkdir release
mkdir install
```

进入release文件夹，执行

```
cd release
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/opencv-3.4.0/install -D BUILD_TESTS=OFF ..
```

再执行编译

```
make
```

最后，安装opencv包

```
sudo make install 
```

安装好后需要加动态库配置：

```
/bin/bash -c 'echo "/opencv-3.4.0/install/lib64" > /etc/ld.so.conf.d/opencv.conf'
```

加载动态库：

```
ldconfig
```

至此，opencv编译安装完成。

