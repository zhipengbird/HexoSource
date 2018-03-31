---
title: Mac OS X中Apache开启ssl
date: 2016-09-03 13:38:04
tags:
- Appache
- ssl
---

1. 生成主机密钥
    这里会要求输入密码，不输入，直接回车
    ```
    sudo mkdir /private/etc/apache2/ssl
    cd /private/etc/apache2/ssl
    sudo ssh-keygen -f server.key
    ```
    执行过程：
    ```
    localhost:~ yuanpinghua$ sudo mkdir  /private/etc/apache2/ssl
    Password:
    localhost:~ yuanpinghua$ cd /private/etc/apache2/ssl
    localhost:ssl yuanpinghua$ sudo ssh-keygen -f server.key
    Generating public/private rsa key pair.
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in server.key.
    Your public key has been saved in server.key.pub.
    The key fingerprint is:
    SHA256:MX4CY5k2ehZtPjp+SQ3F5Au+RKdYrlpN+ycdI43MlQw root@localhost
    The key's randomart image is:
    +---[RSA 2048]----+
    |         o.      |
    |       + .E      |
    |      X O.oo .   |
    |     + #.* .+    |
    |    . + S=o+     |
    |     o *.** +    |
    |      =.+. o o   |
    |     + .o.. o    |
    |    . ..  .o     |
    +----[SHA256]-----+
    ```
    <!-- more -->
2. 生成证书请求文件
      ```
      sudo openssl req -new -key server.key -out request.csr
      ```
      执行过程：
      ```
      localhost:ssl yuanpinghua$ sudo openssl req -new -key server.key -out request.csr
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
      Common Name (e.g. server FQDN or YOUR name) []:
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
      ```
3. 生成ssl证书
    用上一步生成的文件生成ssl证书
    ```
    sudo openssl x509 -req -days 365 -in request.csr -signkey server.key -out server.crt
    ```
    到这里，自签名证书就生成好了，下面就开始配置Apache
    *  `/private/etc/apache2/httpd.conf `，编辑这个文件去掉下面三行前面的 '#'
        ```
        LoadModule ssl_module libexec/apache2/mod_ssl.so
        Include /private/etc/apache2/extra/httpd-ssl.conf
        Include/private/etc/apache2/extra/httpd-vhosts.conf
        ```

    *  `/private/etc/apache2/extra/httpd-ssl.conf`，编辑这个文件去掉下面两行前面的 '#'
            ```          
            SSLCertificateFile "/private/etc/apache2/ssl/server.crt"
            SSLCertificateKeyFile "/private/etc/apache2/ssl/server.key"
            ```
    *  `/private/etc/apache2/extra/httpd-vhosts.conf `，编辑这个文件在` 'NameVirtualHost*:80'` 后面添加

        ```
        <VirtualHost *:443>
            SSLEngine on
            SSLCipherSuite ALL:!ADH:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP:+eNULL
            SSLCertificateFile /private/etc/apache2/ssl/server.crt
            SSLCertificateKeyFile /private/etc/apache2/ssl/server.key
            ServerName localhost
            DocumentRoot "/Library/WebServer/Documents"
        </VirtualHost>
        ```
4. 复制凭证和密钥到apache2目录下
  将/private/ect/apache2/ssl中的server.crt,server.key 复制到/private/ect/apache2/下

5. 到这里就配置完了，检查配置，没问题的话重启Apache就好了
    ```   
    sudo apachectl configtest 检查配置
    sudo apachectl -k restart 强制重启
    ```

6.  在浏览器中输入：可以正常访问
    https://localhost/ , https://192.168.0.1/ ,http://localhost/ , http://192.168.0.106/
