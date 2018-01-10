# IntelliJ OpenCV 开发环境搭建

Windows下的IntelliJ + OpenCV开发环境搭建

### 基于IntelliJ IDEA 15 和 OpenCV 3.1.0

- 在OpenCV官网下载OpenCV安装程序，双击解压到目标目录，譬如 C:\opencv
- 添加系统Path环境变量

```commandline
C:\opencv\build\java\x64
```

- 打开IntelliJ ，选择File->Project Structure->Modules->Dependenciesd

点击加号，选择JAR or directories

选择C:\opencv\build\java\目录下的open-xxx.jar文件

点击确定

测试导入：

```commandline
import org.opencv.core.Core;
import org.opencv.core.CvType;
import org.opencv.core.Mat;
import org.opencv.core.Scalar;
```

只要opencv的相关包都导入成功，则说明配置已经完成

### 在测试中导入opencv包

点击run后报错如下：

```commandline
Exception in thread "main" java.lang.UnsatisfiedLinkError: no opencv_java249 in java.library.path
```

需要在测试中导入opencv包

步骤：

选择当前测试配置，点击Edit->Configuration->VM options:

插入如下代码

```commandline
-Djava.library.path=C:\opencv\build\java\x64;C:\opencv\build\java\x64
```

`自行根据操作系统对应好x86还是x64版本（如果对应错了会提示不能再x86上运行x64或相反，根据系统修改即可）`
    
### OpenCV目前支持的图像格式

- Windows位图文件 - BMP, DIB；
- JPEG文件 - JPEG, JPG, JPE；
- 便携式网络图片 - PNG；
- 便携式图像格式 - PBM，PGM，PPM；
- Sun rasters - SR，RAS；
- TIFF文件 - TIFF，TIF;
- OpenEXR HDR 图片 - EXR;
- JPEG 2000 图片- jp2。

可以用cvSaveImage函数来实现不同图像格式之间的转换。

JAVA处理图片推荐使用OpenCV的相关功能，个人不推荐使用JAI Media的相关功能



