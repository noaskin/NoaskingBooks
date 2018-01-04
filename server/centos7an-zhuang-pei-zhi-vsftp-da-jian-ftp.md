## 安装

在安装前查看是否已安装vsftpd

```bash
# 查看是否已安装 方法一
[root@localhost ~]# rpm -q vsftpd
vsftpd-3.0.2-21.el7.x86_64

# 查看是否已安装 方法二
[root@localhost ~]# vsftpd -v
vsftpd: version 3.0.2

# 安装 vsftpd
[root@localhost ~]# yum -y install vsftpd
```

## 查看位置

```bash
[root@localhost ~]# whereis vsftpd
vsftpd: /usr/sbin/vsftpd /etc/vsftpd /usr/share/man/man8/vsftpd.8.gz
```



