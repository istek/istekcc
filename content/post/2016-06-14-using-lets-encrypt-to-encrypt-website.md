---
title: '使用Let&#8217;s Encrypt加密WEB网站'
date: 2016-06-14T06:33:26+00:00
layout: post
categories:
  - Linux
tags:
  - SSL
  - "Let's Encrypt"
---
Let’s Encrypt是一家新成立的CA证书机构，它提供了免费的TLS/SSL证书，整个申请证书，安装证书的过程全部自动化，免去了网站站长对于SSL知识的要求以及繁琐的操作，并且，它是开源的。

### 要求 {#}

1. 已注册的域名且有效的A记录到WEB服务器。
2. 启用了SSL的web服务器，比如NGINX，APACHE。

### Let’s Encrypt工作原理

![](https://assets.digitalocean.com/articles/letsencrypt/nginx-letsencrypt.png)
<!--more-->
### <span id="i-2">操作步骤 {#}

为了从git仓库获取程序源代码，需要安装依赖软件
```
# apt-get install git bc
```

克隆仓库到本地
```
# git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt
```

停止web服务器
```
# /etc/init.d/nginx stop
# ss -ltn
```

准备工作

Let’s encrypt的webroot插件会在你的网站根目录下的`/.well-known`目录下放入一个特殊的文件，用来在申请证书时，验证你的网站。所以，将以下小节放入你的网站nginx配置文件中。
```
location ~ /.well-known {  
		allow all;
}
```

申请证书

现在就可以使用let’s encrypt程序申请证书了，可以一个证书申请多个域名，这里我说的是子域名，比如 www.abc.com, blog.abc.com。
```
# cd /opt/letsencrypt/
# ./letsencrypt-auto certonly --standalone -d your_domain.tld -d www.yourdomain.tld
```

![2](https://res.cloudinary.com/the-backyard-of-stanley/image/upload/v1465790723/Obtain-Free-Lets-Encrypt-SSL-Certificate-for-Website_y5trdk.png)

之后，会根据你的操作系统安装一系列的依赖的软件包，之后会提示你输入EMAIL地址，请注意，必须填写真实，有效的email地址，一旦你丢失了私钥，需要EMAIL来恢复你的key。

![3](https://assets.digitalocean.com/articles/letsencrypt/le-email.png)

之后提示你是否同意许可说明，点击回车键（Enter）。

![4](https://assets.digitalocean.com/articles/letsencrypt/le-agreement.png)

顺利地话，你会看到如下的终端输出信息，信息中包括了你的证书存储路径以及证书过期时间。
```
IMPORTANT NOTES:  
 - If you lose your account credentials, you can recover through
   e-mails sent to sammy@digitalocean.com
 - Congratulations! Your certificate and chain have been saved at
   /etc/letsencrypt/live/example.com/fullchain.pem. Your
   cert will expire on 2016-03-15. To obtain a new version of the
   certificate in the future, simply run Let's Encrypt again.
 - Your account credentials have been saved in your Let's Encrypt
   configuration directory at /etc/letsencrypt. You should make a
   secure backup of this folder now. This configuration directory will
   also contain certificates and private keys obtained by Let's
   Encrypt so making regular backups of this folder is ideal.
 - If like Let's Encrypt, please consider supporting our work by:

   Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
   Donating to EFF:                    https://eff.org/donate-le
```

OKay，现在证书已经申请好了。

证书说明

一旦证书申请成功后，在`/etc/letsencrypt/live/your_domain_name`目录会存有你的证书，一共有四个PEM编码的文件。

* cert.pem: 你的域名证书
* chain.pem: Let’s Encrypt证书链
* fullchain.pem: 域名证书以及证书链，是一个2合1文件
* privkey.pem: 你的证书私钥（一定要保存好，不要给他人）

生成强Diffie-Hellman组

使用openssl工具生成DH[^DH]key
```
# openssl dhparam -out /etc/ssl/certs/dhparam.pem 4096
```

在nginx上配置TLS/SSL证书

编辑虚拟主机的配置文件，增加以下内容：
```
server {  
		listen 443 ssl;

		server_name example.com www.example.com;

		ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
		ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

		ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
		ssl_prefer_server_ciphers on;
		ssl_dhparam /etc/ssl/certs/dhparam.pem;
		ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128-SHA256:AES256-SHA256:AES128-SHA:AES256-SHA:AES:CAMELLIA:DES-CBC3-SHA:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK:!aECDH:!EDH-DSS-DES-CBC3-SHA:!EDH-RSA-DES-CBC3-SHA:!KRB5-DES-CBC3-SHA';
		ssl_session_timeout 1d;
		ssl_session_cache shared:SSL:50m;
		ssl_stapling on;
		ssl_stapling_verify on;
		add_header Strict-Transport-Security max-age=15768000;

		location ~ /.well-known {
				allow all;
		}

		# The rest of your server block
		root 你的网站根目录;
		index index.html index.htm;

		location / {
				# First attempt to serve request as file, then
				# as directory, then fall back to displaying a 404.
				try_files $uri $uri/ =404;
				# Uncomment to enable naxsi on this location
				# include /etc/nginx/naxsi.rules
		}
}
```

保存并退出，这个配置中指定了申请的证书以及证书私钥，SSL配置项中定义了使用最安全的协议和算法。其中，域名和证书目录需要根据你的证书所在路径修改。

让HTTP网站重定向到HTTPS网站,编辑nginx网站配置文件，按照如下修改:
```
server {  
	listen 80;
	server_name example.org www.example.org;
	location / {
		return 301 https://$host$request_uri;
	}
}
```

启动NGINX并验证 OK，已经配置完成了，可以重启nginx了。
```
/etc/init.d/nginx start
```

访问[SSL Labs](https://www.ssllabs.com/ssltest/analyze.html)验证你的HTTPS是否安全。

### 自动更新证书

Let’s Encrypt的证书有效期是90天，这就需要我们自己进行证书更新的操作，而Let’s Encrypt软件本身是不能自动更新证书的，可以手工更新或者使用脚本自动更新证书。

**手工更新：**
```
/opt/letsencrypt/letsencrypt-auto renew
```

上面的命令可以更新所有的证书有效日期。

**自动更新：**

通过cron任务可以定期的执行上面的脚本以达到自动更新的目的。
```
crontab -e  
```

打开crontab编辑界面，定义每周一的2:30更新一次证书，2:35重载nginx。
```
30 2 * * 1 /opt/letsencrypt/letsencrypt-auto renew >> /var/log/le-renew.log  
35 2 * * 1 /usr/bin/systemctl reload nginx  
```

### 更新Let’s Encrypt客户端

当Let’s Encrypt有新版本时，可以使用下面的命令更新本地软件。
```
git pull  
```



[^DH]: Diffie-Hellman:一种确保共享KEY安全穿越不安全网络的方法，它是OAKLEY的一个组成部分。Whitefield与Martin Hellman在1976年提出了一个奇妙的密钥交换协议，称为Diffie-Hellman密钥交换协议/算法(Diffie-Hellman Key Exchange/Agreement Algorithm).这个机制的巧妙在于需要安全通信的双方可以用这个方法确定对称密钥。然后可以用这个密钥进行加密和解密。但是注意，这个密钥交换协议/算法只能用于密钥的交换，而不能进行消息的加密和解密。双方确定要用的密钥后，要使用其他对称密钥操作加密算法实际加密和解密消息。


