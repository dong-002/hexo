---
title: git的简单使用
date: 2022-10-06 17:39:53
tags: git
categories: 技能学习
---
Git是一个分布式版本控制系统，分布式就是每个客户端都保存版本，当然服务器上也保存版本，但是当服务器发生问题时可以从客户端得到完整版本，不用担心版本丢失。

---

设置账号名和邮箱，在将文件提交到仓库中时需要提供账号和邮箱，记录谁干了什么。

```
git config --global user.name "账号"
git config --global user.email "邮箱"
```

**与Gitee绑定**

* 生成密钥： `ssh-keygen -t rsa` 输入之后按下三次回车

  ![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220726230657692.png)

* 查看密钥： `cat ~/.ssh/id_rsa.pub`

  ![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220726230729155.png)

* Gitee中的ssh公钥：登录码云，将上面的密钥复制到ssh公钥中

  ![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220726230757326.png)

  这样本地仓库就和Gitee绑定了。

Git的仓库可以从其他服务器上克隆一个或者将本地的一个文件夹作为新的仓库。

* 克隆：git clone +远程仓库地址。将Gitee中的仓库地址复制，这样就把远程仓库复制到了本地

  ![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220726230808406.png)

  * 新的仓库：
    1. 首先 git init 将本地仓库初始化
    2. git remote add origin +远程仓库地址。这样本地仓库就和远程仓库关联起来

  #### Git的一些简单命令

1. 将工作区文件添加到暂存区：git add +文件名 | git add .是全部文件

2. 将暂存区文件添加到Git仓库：git commit -m "这里是对提交文件的描述，可以不写"

3. 连接远程仓库：git remote add origin +远程仓库地址

4. 将本地仓库中的文件提交到远程仓库(要在关联的情况下)：`git push origin master`

5. 查看状态：`git status | git status -s` 是简洁查看

6. 查看提交记录：`git log`

   ![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/image-20220726230814664.png)

   上面划线的是提交版本ID

7. 版本回退：

   * `git reset --hard head^` 回退到上个版本
   * `git reset --hard head^^` 回退到上上个版本
   * git reset --hard +版本ID 回退到确定的版本

8. 在回退版本中查看提交记录：`git reflog`

9. 撤销工作区中修改：git checkout --文件名

10. 取消暂存区中文件：git reset HEAD +文件名

11. 从工作区直接到git仓库：git commit -a -m "描述"

12. 删除文件：

    * git rm -f 文件名   删除工作区和仓库的文件
    * git rm --cached 文件名   只删除仓库文件

13. 忽略文件：创建一个名为.gitignore的文件，在这个文件中定义不用提交的文件，提交时就不会提交对应的文件。

13. 查看连接的远程仓库：`git remote -v`

#### 分支操作

开发项目时首先在master主分支创建自己的分支，自己负责的部分完成后将自己的分支合并到master主分支，这样每个人的工作没有相互影响。

* 查看分支情况：git branch    前面有*的表示当前所在分支
* 创建分支：git branch 名字   在创建完分支后还是在master分支中
* 切换分支：git checkout 分支名
* 创建并切换分支：git checkout -b 分支名
* 合并分支：首先切换到master分支，然后执行git merge 分支名
* 删除分支：git branch -d 分支名
* 合并分支冲突：手动解决冲突，然后git add . 和git commit -m "解决了冲突"

#### 远程分支

* 将分支提交到远程仓库：git push -u origin 分支名
* 查看远程分支：git remote show
* 将远程分支下载到本地：git checkout 分支名
* 删除远程分支：git push origin --delete 分支名

#### pull操作

`git pull`是从GitHub上下拉代码到本地仓库。`git clone`不也是可以从仓库中下载代码吗？这两者是有区别的，`git pull`是当你连接了仓库时可以使用，`git pull`是下拉最新的代码到本地；`git clone`是可以从所有仓库下载代码，就算你没有连接上仓库。

下面有一个`git pull`的使用场景

![](https://dong-image.oss-cn-guangzhou.aliyuncs.com/image/%E7%A9%BA%E7%99%BD.png)