# OpenCV3.3.0配置Windows&Linux

最近写了一个J2EE的项目，其中用到了OpenCV的相关内容，所以下载了一个OpenCV库，并且在Windows下面做了开发测试，话不多说，这里直接给下载链接

[https://opencv.org/opencv-3-3.html](https://opencv.org/opencv-3-3.html)

。在这个链接页面中，可以找到不同平台下的OpenCV库，分别是windows、iOS、安卓以及源码包，如下图：

![](http://img.blog.csdn.net/20171202121929113?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzY4NTkwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

## 1. windows下使用OpenCV

首先简单说一下在windows下面使用OpenCV，这个相关的内容和帖子也比较多，我就不赘述，直接说操作流程就行，也算是对大家工作的一个小总结，跟大家看的其与相关帖子大同小异。下载windows下的OpenCV自解压库，即上图中的第一个红色方框，下载之后安装（其实也就是解压缩），就能得到一个文件夹，进入该文件夹，可以得到如下的一些子文件夹：

其中sources文件夹里面是源代码，跟我们从之前下载链接中第四个红色方框下载的内容是一样的；还有一个是build文件夹，可以理解为windows下面编译安装之后的OpenCV库。进入build文件夹，可以发现以下几个子文件夹

![](/assets/import.png)

到了这里基本上大家就能找到自己想要的东西了，因为我自己写的是J2EE的项目，所以我直接进入java文件夹，内容如下图：

![](http://img.blog.csdn.net/20171202123738838?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzY4NTkwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

发现了opencv-330.jar包，毫无疑问，需要在项目中 add build path；此时就可以使用一些基本的函数了，这里我贴出一份官方案例，也就是之前图中 sources\samples\java\ant\src 这个路径下的java文件，代码如下：

```
import org.opencv.core.Core;
import org.opencv.core.Mat;
import org.opencv.core.CvType;
import org.opencv.core.Scalar;

class SimpleSample {

  static{ System.loadLibrary(Core.NATIVE_LIBRARY_NAME); }

  public static void main(String[] args) {
    System.out.println("Welcome to OpenCV " + Core.VERSION);
    Mat m = new Mat(5, 10, CvType.CV_8UC1, new Scalar(0));
    System.out.println("OpenCV Mat: " + m);
    Mat mr1 = m.row(1);
    mr1.setTo(new Scalar(1));
    Mat mc5 = m.col(5);
    mc5.setTo(new Scalar(5));
    System.out.println("OpenCV Mat data:\n" + m.dump());
  }

}
```

但是系统运行之后会报错【no opencv\_java330 in java.library.path】，也就是没有在 java.library.path 里面找到 opencv\_java330 这个库。我们只需要将这个库引入到 java.library.path 即可，说到这儿，顺便补充一句，可能有的人会问 java.library.path 是什么，大家不同的电脑上面配置可能不太一样，可以直接在程序中打印输出 java.library.path ，代码如下

```
System.out.println("Java Library Path"+System.getProperty("java.library.path"));
```

知道了java.library.path之后，我们发现java下面还有两个文件夹，分别是x64和x86，不用多说，分别对应64位和32位的 windows 系统，两个文件夹下面都只有一个文件，就是**opencv\_java330.dll**，也就是我们需要的动态链接库，将这个文件粘贴复制到 java.library.path下面去即可，通常也就是 %JAVA\_HOME%/bin下面，有的人也会放在 %JAVA\_HOME%/jre/bin下面，这个都可以，我测试了不影响。

**其实看完在windows下面使用OpenCV的流程不难想象，opencv-330.jar算是opencv针对java开发的一套接口，这一套接口的背后是调用 opencv\_java330.dll 里面的函数来完成具体的工作。那么如果将工程项目部署到linux下面去，一样也应该有这么一个jar包和类似于dll一样的库函数，知道了这一点，接下来就能理解在linux下面安装和使用OpenCV了。**



## 2. Linux下使用OpenCV

我在windows下面开发了J2EE项目之后打包成war文件，然后发布到Linux的tomcat服务器上部署，当时觉得jar包应该是平台通用的，但是我没有引入dll，所以程序应该无法启动，会报错，果不其然，报错如下【**java.lang.UnsatisfiedLinkError: no opencv\_java330 in java.library.path**】，于是我按照我在windows下面的思路，直接把 opencv\_java330.dll 复制到我在linux下面的java.library.path里面去了（现在看来，真是闹了个笑话，jar包确实是跨平台的，但是没说 dll 也是跨平台的啊，简直naive啊），**错误依旧**。这个时候也就能理解为什么之前的下载链接中强调Windows self-extracting archive，因为下载下来的工具只能在windows下面使用，在linux下面需要自己编译和安装，而下载中提供了源代码，接下来就需要根据源码来编译安装OpenCV了。

第一步，下载源码包，即第一幅图中的第四个链接；  
第二步，解压缩，`tar -zxvf opencv-3.3.0.tar.gz`，会得到一个opencv-3.3.0的文件夹  
第三步，cmake编译。说到这儿，需要强调一下，cmake 编译之前可以提前准备一下必要的工具包，诸如安装java/python等，如果大家不知道准备什么工具也不要紧，假设大家都已经准备好了必要的安装工具，直接开始编译，代码如下

```
cd opencv-3.3.0
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/home/software/opencv ..
```

cmake的参数就不赘述了，也就是根据源码编译并且安装到 /home/software/opencv 这个目录下面，编译完成之后会打印输出一个清单，也就是会安装哪些东西，安装到哪个目录，类似于Windows下面正式安装之前的一个显示页面，我在这个页面吃了一个大亏，因为我需要安装opencv的java相关库，而第一次安装的时候我的系统里面没有安装ant，所以清单里面显示java 一栏中**ant**后面显示**NO**，就这个东西导致我安装虽然成功了，但是仍然没有我想要的opencv\_java330库，所以这里也提醒一下大家，可以根据自己需要的环境来安装对应的库，如果缺失了就及时补充对应的工具包，然后再安装，也就是在 make 和 make install 之前要确保 cmake 运行之后的清单里面要有自己想要的东西。然后我补充安装 ant ，重新 cmake 编译一次，这一次输出打印的清单里面java 后面的所有内容都是 YES 了。

第四步：make编译。为了加快编译，可以多核编译`make -j7`  
第五步：安装`make install`

安装完成之后到安装目录

```
cd /home/software/opencv
```

可以发现4个文件夹，如下图：

![](http://img.blog.csdn.net/20171202132535036?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzY4NTkwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

bin下面是一些基本的工具，lib是库，include下面包含了opencv和opencv2，进入到share文件夹下面

```
cd share
```

有一个 OpenCV 文件夹，进入OpenCV文件夹

```
cd OpenCV
```

可以发现如下的一些文件夹：  
![](http://img.blog.csdn.net/20171202132938560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzY4NTkwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

有java文件夹，进入该文件夹，发现了如下的两个文件：  
![](http://img.blog.csdn.net/20171202133039032?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMzY4NTkwMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast "这里写图片描述")

功夫不负有心人，对应的jar包和库文件全部都有了，jar包和windows下面的jar包是通用的，只需要将**libopencv\_java330.so**拷贝到linux系统下的java.library.path下面去即可。



参考链接：

[http://www.mamicode.com/info-detail-2002782.html](http://www.mamicode.com/info-detail-2002782.html)

[http://blog.csdn.net/lu\_android/article/details/78583275](http://blog.csdn.net/lu_android/article/details/78583275)

[http://blog.csdn.net/XinyanH/article/details/78365305](http://blog.csdn.net/XinyanH/article/details/78365305)

