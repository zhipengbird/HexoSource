---
title: git 命令
date: 2017-07-20 13:23:52
categories: Git
tags: 
- git
---
1. 移除本地仓库的远程git 
使用 `git remote rm`命令解除本地创建与远程git的绑定关系
`git remote rm`命令需要传入一个参数 `远程仓库名`
示例：
{% codeblock 查看当前的远程仓库  lang:ruby   %}
$git remote -v 
origin	git@192.168.250.71:yuanph/HookArray.git (fetch)
origin	git@192.168.250.71:yuanph/HookArray.git (push)
{% endcodeblock %}
{% codeblock 移除远程git  lang:ruby   %}
$ git remote rm origin
{% endcodeblock %}
{% codeblock 添加新远程仓库  lang:ruby   %}
$git remote add origin git@192.168.250.71:shareCode/HookArray.git
{% endcodeblock %}
{% codeblock 查看当前的远程仓库  lang:ruby   %}
$git remote -v 
origin	git@192.168.250.71:shareCode/HookArray.git (fetch)
origin	git@192.168.250.71:shareCode/HookArray.git (push)
{% endcodeblock %}






