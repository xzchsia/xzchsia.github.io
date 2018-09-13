---
layout:     post
title:      "git-ssh 配置和使用以及搭配使用TortoiseGit"
subtitle:   ""
date:       2018-08-28
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 工具
    - git 
---


## Git-TortoiseGit 配置和使用  

#### 1、设置Git的user name和email

```bash  
    $ git config --global user.name "xxx"  

    $ git config --global user.email "xxx@yyy.com"  
```

#### 2、生成密钥

```bash  
    $ ssh-keygen -t rsa -C "xxx@yyy.com"  
```

连续3个回车。如果不需要密码的话。最后得到了两个文件：id_rsa和id_rsa.pub。

#### 3、登陆Github, 添加 ssh 

把id_rsa.pub文件里的内容复制到github主页的setting里面的 SSH and GPG keys 里面添加

#### 4、配合TortoiseGit使用  

使用TortoiseGit的话，可以setting里面的network的SSHClient里面选择git的ssh，新版本的git地址在\Git\usr\bin\ssh.exe

