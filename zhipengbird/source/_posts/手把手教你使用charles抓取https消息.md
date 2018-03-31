---
title: 手把手教你使用charles抓取https消息
date: 2016-09-03 15:33:43
tags:
- https 抓包
---
1. 安装charles
2. 安装sslproxy证书
  * 电脑端（Mac）：  charles-->help-->SSL Proxying --> install charles Root certificate
  在密钥管理中将该证书设置信任。
  * 手机端（iOS）：
      将网络代理设置成电脑网络，然后在safari中输入“http://charlesproxy.com/getssl”下载证书并安装

3. 制作对应用网站的crt证书 这里以"https://cf.ushareit.com"为例
<!-- more -->
#  制作流程：
1. 生成密钥
    打开终端，输入以下命令：
    ```js
    cd /private/etc/apache2/ssl //进行该文件夹中
    sudo ssh-keygen -f server.key //生成key ,若要求输入密码，可以按Enter进入下一步
    ```
2. 用上面生成的server.key 生成证书请求文件
      输入以命令：
      ```js
      sudo openssl req -new -key server.key -out cf.ushareit.com.csr
      ```
      执行记录：
      ```js
      localhost:ssl yuanpinghua$ sudo openssl req -new -key server.key -out cf.ushareit.com.csr
                Password:
                You are about to be asked to enter information that will be incorporated
                into your certificate request.
                What you are about to enter is what is called a Distinguished Name or a DN.
                There are quite a few fields but you can leave some blank
                For some fields there will be a default value,
                If you enter '.', the field will be left blank.
                -----
                Country Name (2 letter code) [AU]:
                State or Province Name (full name) [Some-State]:
                Locality Name (eg, city) []:
                Organization Name (eg, company) [Internet Widgits Pty Ltd]:
                Organizational Unit Name (eg, section) []:
                Common Name (e.g. server FQDN or YOUR name) []:cf.ushareit.com //站点的域名
                Email Address []:

                Please enter the following 'extra' attributes
                to be sent with your certificate request
                A challenge password []:
                An optional company name []:

      ```
3. 使用上述生成的server.key 和证书请求文件生成ssl 证书:
        输入如下命令：
        ```js
        sudo openssl x509 -req -days 365 -in cf.ushareit.com.csr -signkey server.key -out cf.ushareit.com.crt
        ```
        执行记录：
        ```js
        localhost:ssl yuanpinghua$     sudo openssl x509 -req -days 365 -in cf.ushareit.com.csr -signkey server.key -out cf.usharit.com.crt
        Signature ok
        subject=/C=AU/ST=Some-State/O=Internet Widgits Pty Ltd/CN=cf.ushareit.com
        Getting Private key
        ```
4. 到此证书已经制作完成，接下来要进行Appache网络配置：
        使用编辑器打开`/etc/apache2/httpd.conf`文件 将下面两行前的`#`号去掉,后保存。
        ```js
        # LoadModule php5_module libexec/apache2/libphp5.so
        # LoadModule hfs_apple_module libexec/apache2/mod_hfs_apple.so
        ```
        打开`/etc/apache2/extra/httpd-vhosts.conf`文件，在文件末尾添加如下内容：
        ```js
        <VirtualHost *:80>
            DocumentRoot "/Library/WebServer/Documents/cf_ushareit_com"
            ServerName cf.ushareit.com  
            RewriteEngine on
            RewriteRule ^(.*)$ /abc.php
            ErrorLog "/private/var/log/apache2/dummy-cm.ushareit.com-error_log"
            CustomLog "/private/var/log/apache2/dummy-cm.ushareit.com-access_log" common
        </VirtualHost>

        <VirtualHost *:443>
          SSLEngine on
          SSLCipherSuite ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP:+eNULL
          SSLCertificateFile /private/etc/apache2/ssl/cf.ushareit.com.crt
          SSLCertificateKeyFile /private/etc/apache2/ssl/server.key
          DocumentRoot "/Library/WebServer/Documents/cf_ushareit_com"
          ServerName cf.ushareit.com   
          RewriteEngine on
          RewriteRule ^(.*)$ /abc.php
        </VirtualHost>
        ```
        在`/Library/WebServer/Documents/`站点下创建`cf_ushareit_com`文件夹。同时创建一个  `crt`文件夹，用来存放刚创建的`cf_ushareit_com.crt`证书
5. 在命令行中输入以下命令，检查刚加入的文件进行语法检查，并重新启动Appache:

       ```js
       sudo apachectl configtest //检查配置
       sudo apachectl -k restart  //强制重启
       ```
6. 证书安装与sslproxy设置
        在手机端安装刚才创建的crt证书。
        在charles中进行ssl设置：
        ```
        charles -->Proxy-->SSL Proxying Setting --> add -->Host:cf.ushareit.com /port:443 --> ok
        ```
        现在可以尽情使用了！哈哈
