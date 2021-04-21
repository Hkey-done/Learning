MySQL启动服务器无响应问题：在MySQL解压包bin文件中添加vcruntime140_1.dll.html文件

访问 https://cn.dll-files.com/vcruntime140_1.dll.html，下载VCRUNTIME140_1.DLL最新的版本

管理员权限运行CMD，进入到安装包bin文件目录下

初始化数据库：mysqld --initialize --console

安装： mysqld --install

启动： net start mysql

停止：net stop mysql

进入MySQL命令模式：mysql -u root -p

修改密码：ALTER USER root@localhost IDENTIFIED BY ‘密码’

删除MySQL： mysqld --remove mysql

​						sc delete mysql

