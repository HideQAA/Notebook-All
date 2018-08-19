# MySQL8.0.12 安装及配置 #
---


[https://blog.csdn.net/missing2011/article/details/81474857](https://blog.csdn.net/missing2011/article/details/81474857)


配置文件不需要


cd到mysql的bin目录下、mysqld --initialize-insecure --user=mysql自动创建data文件、mysqld --install安装mysql服务，再net start mysql，便能启动服务了

mysql -u root -p 出现输入密码时回车便能进入mysql配置页面

alter user 'root'@'localhost'IDENTIFIED BY 'MyNewPass'，修改密码

---


# 干净卸载MYSQL #
---

[https://blog.csdn.net/cxy_summer/article/details/70142322](https://blog.csdn.net/cxy_summer/article/details/70142322)