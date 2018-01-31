# 使用docker-maven-plugin插件将项目编译为docker镜像到远程linux服务器



在win10中使用idea开发一个模块化的maven项目，然后想要把该项目直接编译到远程linux服务器的docker中，具体做法如下：

####  1、项目pom.xml文件配置

在各模块中的pom文件中加入以下

```
<plugin>
    <groupId>com.spotify</groupId>
    <artifactId>docker-maven-plugin</artifactId>
    <configuration>
        <imageName>${project.name}:${project.version}</imageName>
        <dockerDirectory>${project.basedir}/src/main/docker</dockerDirectory>
        <skipDockerBuild>false</skipDockerBuild>
        <resources>
            <resource>
                <directory>${project.build.directory}</directory>
                <include>${project.build.finalName}.jar</include>
            </resource>
        </resources>
    </configuration>
</plugin>
```



#### 2、Dockerfile配置

在各模块的src/main下面**新建docker包**，在src/main/docker下面**新建Dockerfile与runboot.sh文件**（文件内容因情况不同就不贴了）

```
FROM frolvlad/alpine-oraclejdk8:slim
VOLUME /tmp
ADD noasking-dota2omg-web-1.0-SNAPSHOT.jar app.jar
RUN sh -c 'touch /app.jar'
ENTRYPOINT [ "sh", "-c", "java $JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -jar /app.jar" ]
```



#### 3、Docker配置

在linux服务器上装好docker，我的linux版本为centos7，这里不建议使用\#yum install docker方式安装，因为这种方式安装的版本比较旧，而且在配置远程api时要好多错误，建议使用方式

\#**curl -fsSL https://get.docker.com/ \| sh**或\#yum install docker-engine,这里以17.0.3-ce版本为例，使用

\#docker version命令即可验证是否安装成功，接下来要想在windows中操作远程linux中的docker，那前提是必须**  
**

**开启docker远程API**，修改docker配置文件\#**vi /usr/lib/systemd/system/docker.service**   ,进入编辑模式后，将ExecStart这一行后面加上**-H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock**，改完后如下所示

**ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix:///var/run/docker.sock**  ，这里就写4个0，你可别改成自己的ip哦，保存后退出，重新加载配置文件\#systemctl daemon-reload   ,启动docker \#systemctl start docker  ,

输入\#netstat -anp\|grep 2375 显示docker正在监听2375端口，输入\#**curl 127.0.0.1:2375/info**  显示一大堆信息，证明远程api就弄好了  


#### 4、Window配置

在windows系统环境变量中新建**DOCKER\_HOST**,值为**tcp://10.100.74.220:2375**，（你改成你自己的docker服务器ip地址）

![](http://img.blog.csdn.net/20170405151259717?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHZ5dWFuMTIzNA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

####  5、打包

打开dos窗口，即cmd命令行，进入到你要编译的项目文件夹下，

输入**mvn clean package docker:build -DskipTests**,然后慢慢等待，直到最后build成功



#### 6、确认

登陆linux，输入\#docker images 发现自己的项目已经被编译成镜像了，可以启动容器运行镜像了，也相当于完成了项目的**云部署**



