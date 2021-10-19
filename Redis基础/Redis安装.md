前言
不建议使用apt-get方式安装redis，因为redis的路径不好找，修改配置比较麻烦。所以建议使用下载安装包解压的方式。

安装redis需要提前安装c++

	sudo apt-get install gcc
	sudo apt-get install g++
安装完后查看是否成功

	gcc --version
	g++ --version

安装过程


1. 下载redis

	sudo wget http://download.redis.io/releases/redis-6.0.8.tar.gz

2. 解压安装包

	sudo tar -zxvf redis-6.0.8.tar.gz

3. 进入安装目录

	cd redis-3.2.6

4. 编译redis，时间较长

	make

5. 安装

	sudo make install PREFIX=/usr/local/redis

6. 进入安装目录

	cd /usr/local/redis/bin

7. 启动redis-server

	./redis-server

成功！

配置redis
以上运行的是前台的redis，使用不便，故改为后台redis运行。

1. 拷贝一个配置文件，先进入解压目录

	cd /home/pyvip/redis-6.0.8

2. 拷贝redis.conf到安装目录

	sudo cp redis.conf /usr/local/redis/bin

3. 进入安装目录

	cd /usr/local/redis/bin

4. 修改配置文件

	sudo vim redis.conf

	修改内容：

 	bind 127.0.0.1 ---> bind 0.0.0.0

	daemonize no ---> daemonize yes

	requiredpassword 改为 自定义密码如 requiredpassword 123456

5. 启动redis-server

	./redis-server redis.conf

6. 启动redis

	./redis-cli -p 6379 -a 123456(自己的密码)
