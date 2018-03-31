---
title: Mac上 Appache 的操作
date: 2016-09-02 23:59:01
tags:
- Appache
---

# 常用操作：
1. 打开终端，运行启动Apache命令：
```
sudo apachectl start

```
2. 关闭命令：
```
sudo apachectl stop
```
3. 重启命令
```
sudo apachectl restart
```
4. 查看Apache版本命令
```
httpd –v
```
5. 语法检测
```
apachectl configtest
```
6. 查看Apache版本号：
```
httpd -v
```
<!-- more -->
# Root目录

启用Apache之后，可以直接在浏览器中访问`http://localhost`，如果出现”It works!”就表示运正常。
启用Apache之后，首先得知道网页文件应该放在哪个目录才能正常运行。Mac OS X 中默认有两个目录可以直接运行Web程序，一个是系统级的根目录，一个是用户级的根目录。
* 系统级的根目录是：
  ```
  /Library/WebServer/Documents/

  ```
  它对应的网址是:
  ```
  http://localhost
  ```
* 用户级的根目录是:
  ```
  ~/Sites
  ```
  这里需要注意的~/Sites也就是你用户目录下面的”站点”目录，在OS X 10.8以后，这个目录可能没有，所以你需要手动建立一个同名目录。建立方式很简单，直接在终端中运行：
  ```
  sudo mkdir ~/Sites
  ```
  或者可以直接在目录中新建Sites文件夹

## 配置用户级目录
  1. 创建有户目录“Sites”文件夹，找开终端：
    ```
    sudo mkdir ~/Sites
    ```
  2. 进入 /etc/apache2/users/
    ```
    cd /etc/apache2/users/
    sudo vim username.conf

    ```
    添加内容为：
    ```
    <Directory "/Users/yuanpinghua/Sites/">
      AllowOverride All
      Options Indexes MultiViews FollowSymLinks
      Require all granted
    </Directory>
    ```
    上行中的`yuanpinghua`就是用户名，然后将该文件权限改为：644
    ```
    sudo chmod 644 username.conf
    ```
    3. 进到/etc/apache2/目录，sudo vim httpd.conf 将下面三句话的注释去掉：
    ```
      LoadModule authz_core_module libexec/apache2/mod_authz_core.so
      LoadModule authz_host_module libexec/apache2/mod_authz_host.so
      LoadModule userdir_module libexec/apache2/mod_userdir.so
      Include /private/etc/apache2/extra/httpd-userdir.conf
    ```
    将这四句前的“#”去掉
    4. 进到/etc/apache2/extra/目录，
      ```
      sudo vim httpd-userdir.conf
       ```
      将`Include /private/etc/apache2/users/*.conf `这句话放开注释。
    5. 然后终端输入：`sudo apachectl restart `重启apache，浏览器输入：` loacal/~yuanpinghua/` 就可以正常显示该目录下的文件


参考网页：
[在Mac上配置Apache+PHP环境](http://www.aichengxu.com/view/4587749)
[Mac OS X 上的Apache配置](http://www.jianshu.com/p/7b8d5d6f22c9)
[Mac OS X EI Captitan 中Apache开启SSL (HTTPS)](http://www.aichengxu.com/view/10044030)
[Mac OS X中Apache开启ssl](http://www.cnblogs.com/y500/p/3596473.html)
