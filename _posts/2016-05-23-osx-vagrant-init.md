---
layout: post
title: "OSX配置Vagrant"
description: "OSX配置Vagrant虚拟环境"
category: tools
tags: [tools, OSX, Vagrant]
---

##配置步骤

###安装VirtualBox

虚拟系统运行在VirtualBox中，类似的工具还有VMware，但后者是收费的。

[VirtualBox下载地址](https://www.virtualbox.org/wiki/Downloads).

它支持多个平台，请根据自己的情况选择对应的版本。

###安装Vagrant

[Vagrant下载地址](https://www.vagrantup.com/downloads.html).

选择最新的版本即可。 检查安装是否成功，运行命令：vagrant

查看安装版本，运行命令：
    vagrant －v

至此，基本的工具已经安装完成了。

###初始化Vagrant

1.安装完成后，在想要搭建环境的目录下执行命令vagrant init

2.复制files和scripts到当前目录
3.然后修改或替换Vagrantfile文件

    Vagrantfile
    # -*- mode: ruby -*-
    # vi: set ft=ruby :

    # Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
    VAGRANTFILE_API_VERSION = "2"

    Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
      # All Vagrant configuration is done here. The most common configuration
      # options are documented and commented below. For a complete reference,
      # please see the online documentation at vagrantup.com.

      # Every Vagrant virtual environment requires a box to build off of.
      config.vm.box = "hashicorp/precise32"

      # The url from where the 'config.vm.box' box will be fetched if it
      # doesn't already exist on the user's system.
      config.vm.box_url = "./files/boxes/precise32.box"

      # Create a forwarded port mapping which allows access to a specific port
      # within the machine from a port on the host machine. In the example below,
      # accessing "localhost:8080" will access port 80 on the guest machine.
      config.vm.network "forwarded_port", guest: 80, host: 8811

      # Create a private network, which allows host-only access to the machine
      # using a specific IP.
      # config.vm.network "private_network", ip: "192.168.33.10"

      # Create a public network, which generally matched to bridged network.
      # Bridged networks make the machine appear as another physical device on
      # your network.
      # config.vm.network "public_network"

      # If true, then any SSH connections made will enable agent forwarding.
      # Default value: false
      # config.ssh.forward_agent = true

      # Share an additional folder to the guest VM. The first argument is
      # the path on the host to the actual folder. The second argument is
      # the path on the guest to mount the folder. And the optional third
      # argument is a set of non-required options.
      config.vm.synced_folder "./projects", "/projects"
      
    $script = <<SCRIPT
    sudo -s
    source /home/vagrant/.profile
    /bin/bash /vagrant/scripts/init.sh
    SCRIPT

      config.vm.provision "shell", inline: $script

      # Provider-specific configuration so you can fine-tune various
      # backing providers for Vagrant. These expose provider-specific options.
      # Example for VirtualBox:
      #
      config.vm.provider "virtualbox" do |vb|
        # Don't boot with headless mode
        vb.gui = false

      #  # Use VBoxManage to customize the VM. For example to change memory:
      #   vb.customize ["modifyvm", :id, "--memory", "1024"]
      end
      #
      # View the documentation for the provider you're using for more
      # information on available options.

      # Enable provisioning with CFEngine. CFEngine Community packages are
      # automatically installed. For example, configure the host as a
      # policy server and optionally a policy file to run:
      #
      # config.vm.provision "cfengine" do |cf|
      #   cf.am_policy_hub = true
      #   # cf.run_file = "motd.cf"
      # end
      #
      # You can also configure and bootstrap a client to an existing
      # policy server:
      #
      # config.vm.provision "cfengine" do |cf|
      #   cf.policy_server_address = "10.0.2.15"
      # end

      # Enable provisioning with Puppet stand alone.  Puppet manifests
      # are contained in a directory path relative to this Vagrantfile.
      # You will need to create the manifests directory and a manifest in
      # the file default.pp in the manifests_path directory.
      #
      # config.vm.provision "puppet" do |puppet|
      #   puppet.manifests_path = "manifests"
      #   puppet.manifest_file  = "site.pp"
      # end

      # Enable provisioning with chef solo, specifying a cookbooks path, roles
      # path, and data_bags path (all relative to this Vagrantfile), and adding
      # some recipes and/or roles.
      #
      # config.vm.provision "chef_solo" do |chef|
      #   chef.cookbooks_path = "../my-recipes/cookbooks"
      #   chef.roles_path = "../my-recipes/roles"
      #   chef.data_bags_path = "../my-recipes/data_bags"
      #   chef.add_recipe "mysql"
      #   chef.add_role "web"
      #
      #   # You may also specify custom JSON attributes:
      #   chef.json = { mysql_password: "foo" }
      # end

      # Enable provisioning with chef server, specifying the chef server URL,
      # and the path to the validation key (relative to this Vagrantfile).
      #
      # The Opscode Platform uses HTTPS. Substitute your organization for
      # ORGNAME in the URL and validation key.
      #
      # If you have your own Chef Server, use the appropriate URL, which may be
      # HTTP instead of HTTPS depending on your configuration. Also change the
      # validation key to validation.pem.
      #
      # config.vm.provision "chef_client" do |chef|
      #   chef.chef_server_url = "https://api.opscode.com/organizations/ORGNAME"
      #   chef.validation_key_path = "ORGNAME-validator.pem"
      # end
      #
      # If you're using the Opscode platform, your validator client is
      # ORGNAME-validator, replacing ORGNAME with your organization name.
      #
      # If you have your own Chef Server, the default validation client name is
      # chef-validator, unless you changed the configuration.
      #
      #   chef.validation_client_name = "ORGNAME-validator"
    end



 * 修改配置参数： config.vm.box = “base” 修改为config.vm.box = “hashicorp/precise32”
该参数的含义是虚拟机使用什么样的镜像，默认是base，我们将它修改为Ubuntu precise 32 VirtualBox
 * 打开配置： # config.vm.synced_folder “../data”, “/vagrant_data”
将其修改如下： config.vm.synced_folder “./projects”, “/projects”
该参数的含义是：使用当前目录下的projects目录作为项目目录，它与虚拟机的/projects目录相对应，并且内容保持同步。

###创建项目目录projects
配置文件中用到了当前目录下的projects目录，所以需要创建该目录，不然启动vagrant时会报错。
    
    mkdir ./projects

注：如果你安装Vagrant时使用了sudo，那么之后的所有vagrant命令前都需要加上sudo!!!

###下载镜像
官方封装好的Ubuntu基础镜像：
Ubuntu precise 32 VirtualBox http://files.vagrantup.com/precise32.box
Ubuntu precise 64 VirtualBox http://files.vagrantup.com/precise64.box
如果你要其他系统的镜像，可以来这里下载：http://www.vagrantbox.es/
将下载下来的镜像文件放在与Vagrantfile文件同级目录的file(如果没有需要创建)文件夹中.
目录如下：file/box/precise32.box

###添加配置：
在Vagrantfile文件中的config.vm.box配置之后添加如下配置：
config.vm.box_url = "./files/boxes/precise32.box"
配置完成之后，就可以启动Vagrant了。 
在当前目录执行命令：
    vagrant up
至此，vagrant虚拟环境就启动起来了。在当面目录执行
    vagrant ssh
就能登陆到虚拟系统中。


在虚拟系统的/projects中路中执行touch testfile。 
创建一个测试文件，然后进入到你自己的系统，Vagrantfile所在的目录. 
进入到projects目录中，你会发现存在一个testfile文件。
我想你已经明白了vagrant的精髓之处，你可以在自己的系统中编辑代码. 而在vagrant的虚拟系统中去运行代码，也就是说，你不用在自己的系统中安装运行环境！
Windows 用户注意：Windows 终端并不支持 ssh，所以需要安装第三方 SSH 客户端，比如：Putty、Xshell 等。

####常用命令
    vagrant init  # 初始化
    vagrant halt  # 挂起虚拟机
    vagrant reload  # 重启虚拟机
    vagrant ssh  # SSH 登录至虚拟机
    vagrant status  # 查看虚拟机运行状态
    vagrant destroy  # 销毁当前虚拟机
    vagrant up  # 启动虚拟机
更多内容请查阅[官方文档](http://docs.vagrantup.com/v2/cli/index.html)
至此，一个简单的，垮平台的vagrant开发环境就配置好了。但虚拟机中却什么都没有安装，比如php，mysql，apache等。

###配置ngnix

这篇文章将写点关于虚拟机中Nginx的配置，以及在真实机中访问Nginx的方法。

打开Vagrantfile文件中，找到如下配置：

    config.vm.network "forwarded_port", guest: 80, host: 8080

该配置的意思就是将虚拟机的80端口映射到真实机的8080端口。

使用vagrant ssh命令进入虚拟机

备份默认nginx配置文件

    sudo cp /etc/nginx/nginx.conf /etc/nginx/nginx.conf.back

修改配置

打开/etc/nginx/nginx.conf,将里面的内容更改如下：

    events {
    	worker_connections 1024;
    }
    http {
        server {
            listen 80;
            server_name test.com www.test.com;
            charset utf-8;
            location / {
                root /projects/;
                index index.html index.htm;
            }
            #redirect server error pages to the static page /50x.html
            error_page 500 502 503 504 /50x.html;
            location = /50x.html {
                root /projects/;
            }
        }
    }
添加HTML页面

在虚拟机中：
    cd /projects

在该目录下新建index.html或者index.htm文件，内容如下：

    <html>
        <head>
            <title>R_Lanffy</title>
        </head>
        <body>
            Hello World
        </body>
    </html>

访问测试

在真实机浏览器中输入地址：test.com:8080或者www.test.com:8080即可访问到虚拟机中的nginx相关配置。

如果想达到输入test.com就能访问的目的，是需要将Vagrantfile文件中的8080修改为80

注：如果出现不能访问的情况，很有可能是在启动虚拟机之前，8080端口被占用了。解决办法就是将端口修改为没有被占用的端口。

查看端口是否被监听:
    netstat -an | grep 8080





    nginx.conf
    user www-data;
    worker_processes 4;
    pid /var/run/nginx.pid;

    events {
    	worker_connections 768;
    	# multi_accept on;
    }

    http {

    	##
    	# Basic Settings
    	##

    	sendfile on;
    	tcp_nopush on;
    	tcp_nodelay on;
    	keepalive_timeout 65;
    	types_hash_max_size 2048;
    	# server_tokens off;

    	# server_names_hash_bucket_size 64;
    	# server_name_in_redirect off;

    	include /etc/nginx/mime.types;
    	default_type application/octet-stream;

    	##
    	# Logging Settings
    	##

    	access_log /var/log/nginx/access.log;
    	error_log /var/log/nginx/error.log;

    	##
    	# Gzip Settings
    	##

    	gzip on;
    	gzip_disable "msie6";

    	gzip_vary on;
    	gzip_proxied any;
    	gzip_comp_level 6;
    	gzip_buffers 16 8k;
    	gzip_http_version 1.1;
    	gzip_types text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

    	##
    	# nginx-naxsi config
    	##
    	# Uncomment it if you installed nginx-naxsi
    	##

    	#include /etc/nginx/naxsi_core.rules;

    	##
    	# nginx-passenger config
    	##
    	# Uncomment it if you installed nginx-passenger
    	##
    	
    	#passenger_root /usr;
    	#passenger_ruby /usr/bin/ruby;

    	##
    	# Virtual Host Configs
    	##

    	include /etc/nginx/conf.d/*.conf;
    	include /etc/nginx/sites-enabled/*;
    	include /vagrant/files/nginx/conf.d/*.conf;
    	

    	server {
    		listen 80;
    		server_name walktest www.walktest.com;
    		charset utf-8;
    	
    		index index.html index.htm index.php;
    		root /projects/;
    		location / {
                            try_files $uri $uri/ /index.php?$query_string;
                    }
                    location ~ \.php$ {
                            fastcgi_intercept_errors on;
                            #root           html;
                            fastcgi_pass   127.0.0.1:8899;
                            fastcgi_index  index.php;
                            fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
                            include        fastcgi_params;
    	}

    }

    #mail {
    #	# See sample authentication script at:
    #	# http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
    # 
    #	# auth_http localhost/auth.php;
    #	# pop3_capabilities "TOP" "USER";
    #	# imap_capabilities "IMAP4rev1" "UIDPLUS";
    # 
    #	server {
    #		listen     localhost:110;
    #		protocol   pop3;
    #		proxy      on;
    #	}
    # 
    #	server {
    #		listen     localhost:143;
    #		protocol   imap;
    #		proxy      on;
    #	}
    #}



把ngnix.conf这个文件放进projects文件夹中，然后登录vagrant，将其复制到/etc/nginx中，替换原来的nginx.conf。

    sudo nginx -s reload

然后在你自己的电脑中的/etc/hosts文件中添加：127.0.0.1	www.walktest.com

就可以了，projects文件夹中要有一个index.html文件中

projects文件夹中要有一个index.html文件

浏览器输入 www.walktest.com:8811

就可以看到   Hello World！