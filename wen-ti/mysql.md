# MySQL数据库远程访问权限

#### 1、**改表法**

可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改成"%"







