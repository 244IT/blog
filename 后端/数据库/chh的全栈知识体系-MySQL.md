**1.下载免安装版**

**2.执行以下步骤**

* 跳转到mysql的bin目录下
* 安装mysql的服务：mysqld --install
* 初始化mysql，在这里，初始化会产生一个随机密码,如下图框框所示，记住这个密码，后面会用到(mysqld --initialize --console)

**3.修改密码**

* alter user 'root'@'localhost' identified by '新密码'

