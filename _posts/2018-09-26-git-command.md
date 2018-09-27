---
layout:     post
title:      "Git中相应的command的用法"
subtitle:   ""
date:       2018-09-26 
author:     "Hsia"
header-img: ""
catalog: true
tags:
    - 技术
    - Git
---


## github添加Tags

git tag 处理 添加v3.1.0-ios v3.1.0-android两个不同的tag信息
 
1 . 获取commit：  

```bash
git log --pretty=format:"%h %s" --graph  
```
显示每一次的提交记录信息，方便查找需要打tag的记录

![git-log][git-log]

2 . 给指定的commit打Tag： 

```bash
git tag -a v3.1.0-android 9caa751 -m “v3.1.0-android版本”
git tag -a v3.1.0-ios e296b0f  -m “v3.1.0-ios版本”
```
此为分别打了两个不同提交记录的tag
![git-create-tag][git-create-tag]

3 . 提交tags：  

```bash
git push origin --tags 	# 将本地所有Tag一次性提交到git服务器
```

报错： 
```bash
error: src refspec --tags does not match any.
error: failed to push some refs to 'https**github.com/***/code'
```

4 . 提交单个tag：  

```bash
git push origin v3.1.0-ios     # 操作成功
```

5 . 提交单个tag：  

```bash
git push origin v3.1.0-android     # 操作成功
```
![git-push][git-push]

6 . 查看github上面的log信息    

![git-result][git-result]

7 . 删除本地tag
```bash
git tag -d 标签名  

例如：git tag -d v3.1.0
```

8 . 删除远程标签tag  
```bash
git push origin :refs/tags/标签名  

例如：git push origin :refs/tags/v3.1.0
```
![git-delete-tag][git-delete-tag]


[git-log]:/img/in-post/git-command/1-git-log-show.png
[git-create-tag]:/img/in-post/git-command/2-git-create-tag.png
[git-push]:/img/in-post/git-command/3-git-push-tags.png
[git-result]:/img/in-post/git-command/4-git-result-tags.png
[git-delete-tag]:/img/in-post/git-command/5-git-delete-tag.png

