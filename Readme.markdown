CentOS安装Ruby on Rails可部署环境
=================================

在无数次失败之后终于成功安装配置好了rails，以此为记，希望对你们有所帮助
-----------------------------------------------------------------------

一些基本配置
------------

	## 虚拟机环境
	## system: minimum Centos 6.4
	## user: root
	## 已安装软件 wget、git
	## 配置关闭selinux、iptables
	## 修改/etc/selinux/config文件
	## 将SELINUX=enforcing改为SELINUX=disabled
	## 关闭iptables自启动
	chkconfig iptables off
	## 重启
	reboot

	## 更改yum为163的源（加速）
	## 备份源文件
	mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.bak
	## 下载163源文件
	cd /etc/yum.repos.d/
	wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
	## 替换源文件
	mv CentOS6-Base-163.repo CentOS-Base.repo
	## 更新列表
	yum mekecache
	## 更新系统
	yum update

	## 安装必备软件
	yum install git-core openssl openssl-devel subversion curl curl-devel gcc-c++ patch readline readline-devel zlib zlib-devel libyaml-devel libffi-devel make bzip2 autoconf automake libtool bison sqlite-devel libxml2 libxml2-devel libxslt libxslt-devel libtool nodejs

安装RVM、Ruby、Rails
--------------------

	## 安装RVM
	\curl -L https://get.rvm.io | bash -s stable

	## 将rvm添加进bashrc
	echo '[[ -s "$HOME/.rvm/scripts/rvm" ]] && . "$HOME/.rvm/scripts/rvm"' >> ~/.bashrc
	echo 'PATH=$PATH:$HOME/.rvm/bin' >> ~/.bashrc
	source ~/.bashrc

	## 安装pkg readline
	rvm pkg install readline

	## 安装Ruby
	rvm install 2.1.1 && rvm use 2.1.1 --default

	## 更改gem为淘宝的源（加速）
	gem sources --remove https://rubygems.org/
	gem sources -a http://ruby.taobao.org/

	## 安装Bundler
	gem install bundler

	## 配置gemrc
	echo 'install: --no-rdoc --no-ri' >> ~/.gemrc
	echo 'update:  --no-rdoc --no-ri' >> ~/.gemrc

	## 安装Rails
	gem install rails

	## 找到rails创建新应用的模板目录（/usr/local/rvm/gems/ruby-x.x.x/gems/railties-x.x.x/lib/rails/generators/rails/app/templates/），修改Gemfile头部，以使应用安装更迅速
	## 将source 'https://rubygems.org'改为source 'http://ruby.taobao.org'

安装Nginx、Passenger
--------------------

	## 安装Passenger
	gem install passenger

	## 安装Passenger修改版Nginx
	## 先从可用地址下载nginx-x.x.x.tar.gz备用
	## 修改/etc/hosts文件，将www.nginx.org指向本地可访问的地址，用于下载nginx-x.x.x.tar.gz
	## 执行以下命令
	/usr/local/rvm/gems/ruby-x.x.x/gems/passenger-x.x.x/bin/passenger-install-nginx-module

	## 一路Enter后，选择1，按Enter，将安装路径改为/usr/local/nginx，按ENter，等待安装结束

	## 将nginx程序软连接到/usr/local/bin/nginx下
	ln -s /usr/local/nginx/sbin/nginx /usr/local/bin/nginx

	## 修改nginx.conf的Server部分如下（自行参考更改）
	server {
		listen       80;
		server_name  some-example.com;

		root /path-to-your-application/public;
		passenger_enabled on;
		rails_env development;
	}

	## 将repo中的nginx文件复制到/etc/init.d/中
	## nginx服务常用操作
	service nginx start/stop/restart

	## nginx服务随系统启动
	chkconfig --level 2345 nginx on

	## 进入网站目录（默认为/usr/local/nginx/html）创建新应用
	rails new my_new_website

	## 这时访问网站可能会报错，在网站的db目录下创建一个空文件development.sqlite3即可

测试一下效果吧:)
