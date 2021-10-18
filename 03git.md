---
title: 03 Git学习
date: 2021-02-18 21:16:28
tags: [Git, 总结]
categories: Git
---
简单学了一下Git。

<!-- more -->

## 一、Git简介

1. Git概念：

    Git是一个免费、开源的分布式版本控制系统，可以快速高效的处理从小型到大型的项目。
1. 什么是版本控制：
   
    版本控制是一种记录一个或若干个文件内容变化，以便来查阅特定版本修订情况的系统。

2. 版本控制系统分类：

    - 集中式版本控制系统：

      代表：CVS、SVN、Perforce
      特点：只有一个中央服务器，如果中央服务器宕机，那么无法提交更新。

    - 分布式版本控制系统：

      代表：Git、Bitkeeper
      特点：每个客户端都相当于服务器，都存有历史版本。

<!-- more -->

## 二、Git结构

- 本地库

​      {% asset_img image-20210218213335908.png %}

- 远程库
  
  - 团队内部协作
  
  {% asset_img image-20210218213711622.png %}
  
  - 跨团队协作

​	{% asset_img image-20210218214139960.png %}

- 托管中心分类：

1. 局域网：可以搭建GitLab服务器作为代码托管中心（GitLab自己搭建）
2. 外网：可以使用GitHub或者Gitee作为代码托管中心（不用自己搭建）

## 三、Git命令

（1）配置用户名和邮箱，用于commit时在托管中心的显示信息

```bash
$ git config --global user.name ""
$ git config --global user.email ""
```

（2）本地仓库初始化

```bash
$ git init
```

（3）将工作区数据添加到暂存区

```bash
$ git add demo.txt
$ git add .		// 添加全部文件
```

（4）将暂存区数据添加到本地仓库

```bash
$ git commit -m "message.." demo.txt
```

（5）查看状态

```bash
$ git status
```

（6）查看历史记录

```bash
$ git log
```

{% asset_img image-20210218220506151.png %}

（6）将log信息美化成一行

{% asset_img image-20210218221209607.png %}

```bash
$ git log --pretty=oneline
```

{% asset_img image-20210218221941947.png %}

（7）将log信息美化成一行，并简写哈希值

```bash
$ git log --oneline
```

{% asset_img image-20210218222108472.png %}

（8）在(7)的基础上，添加回退步数

```bash
$ git reflog
```

{% asset_img image-20210218222340732.png %}

（9）历史版本的前进和后退

{% asset_img image-20210218223254635.png %}

- 本地库指针移动后，工作区和暂存区跟着改变

```bash
$ git reset --hard xxx
```

- 本地库指针移动后，工作区不变，暂存区跟着改变

```bash
$ git reset --mixed xxx
```

- 本地库指针移动后，工作区和暂存区不变

```bash
$ git reset --soft xxx
```

（10）将工作区数据和暂存区数据进行比较

- 比较单个文件

```bash
$ git diff [filename]
```

{% asset_img image-20210218225944219.png %}

- 比较所有文件

```bash
$ git diff
```

- 比较暂存区和工作区差别

```bash
$ git diff [历史版本号] [filename]
```

## 四、Git分支

1. 什么是分支？

   在版本控制过程中，使用多条线同时推进多个人物，这里的多条线就是多个分支。

2. 通过一张图展示分支：

{% asset_img image-20210218231830840.png %}

3. 分支的作用

   同时多个分支可以并行开发，互不耽误，互不影响，提高开发效率；

   如果有一个分支功能开发失败，直接删除这个分支就可以了，不会对其它分支产生任何影响。

4. 查看分支

   ```bash
   $ git branch -v
   ```

5. 创建分支

   ```bash
   $ git branch [branchName]
   ```

6. 切换分支

   ```bash
   $ git checkout [branchName]
   ```

7. 合并分支

   ```bash
   $ git checkout master	// 返回主分支
   $ git merge branch01	// 将branch01与主分支合并
   ```

   合并冲突：

   - 当分支之间修改不同文件时，不会产生冲突，可以顺利合并。

   - 如果两个分支都对同一个文件的同一个位置进行了修改，那么合并的时候系统便不知道按照哪个分支合并，所以就会出现冲突问题。

   - 产生冲突问题，会报错，会在冲突文件下产生相应的标记

     {% asset_img image-20210218235210604.png %}

     只要清除其中一个分支的修改，然后再通过git add 和git commit 提交修改，就能解决冲突了。

     *注意，冲突时是不能切换分支的，只有解决冲突。*

## 五、Git远程库

> 通过github/gitee可以创建远程库

（1）查看远程库名

```bash
$ git remote -v
```

（2）给远程库另起别名

```bash
$ git remote add <alias> <repoUrl>
```

（3）将本地库推送到远程库

```bash
$ git push [alias] [branch]
```

{% asset_img image-20210219093249262.png %}

（4）将远程库克隆到本地

```bash
$ git clone <repoUrl>
```

​	克隆操作：

- 初始化本地库
- 将远程库内容完整地克隆到本地
- 自动创建远程库别名

（5）团队协作推送

			- A成员创建了项目，B成员从远程库中克隆项目到本地
			- B成员修改了项目，将项目push到远程库
			- 此时由于A成员没有要求B成员进行开发，所以B成员无法push
			- A成员可以在setting中找到Manage Access，邀请B成员
			- B成员收到邀请链接，就可以push
			- 注意，计算机会记录登录的缓存，可在windows上搜索“凭据”，删除缓存

（6）拉取远程库中的修改

- 拉取操作相当于fetch + merge

  ```bash
  $ git fetch <alias> <branch>
  ```

  将远程库抓取到本地库，但工作区的内容暂未更新；

  抓取后，可以查看远程库中的内容是否修改：

  ```bash
  $ git checkout <alias>/<branch>	// git checkout origin/master 切换到远程库中的分支
  $ cat demo.txt	// 查看文件
  ```

  如果修改和预期一样，将其合并到本地：

  ```bash
  $ git checkout master	// 切换回本地库的分支
  $ git merge <alias>/<branch>	// 将远程库中的分支合并到本地
  ```

- pull操作：

  ```bash
  $ git pull <alias> <branch>	// git pull origin master，将origin远程库中的master分支拉取到本地
  ```

- 两种操作的区别：

  - fetch + merge：步骤更加繁琐，为了安全，保险起见
  - pull：操作简单，一步到位

（7）团队协作开发时的冲突问题

- 如果成员A修改了文件1并push到了远程库，而成员B也修改了文件1的同一位置并push到远程库，那么此时就会产生冲突问题
- 解决冲突：因为成员B是后push的，所以会出错，那么成员B就要将远程库的内容pull下来，然后在文件1中查看冲突情况，选择最佳的处理方案，解决冲突后再push到远程库中
- 注意：解决冲突后的commit不需要接文件名，如：`$ git commit -m "解决了冲突"`

（8）跨团队协作

{% asset_img Snipaste_2021-02-19_10-28-48.png %}

1. C看到了A(个人或者团队)创建的项目，想要修改一些不足
2. C将A的项目fork下来，这样就有一个同样的远程库，然后将远程库克隆到本地
3. C修改之后，push到远程库，但改变的只是fork中的项目，不会对A的项目产生影响
4. 于是，C向A发送一个pull request，A收到pull request，两人进行交流，如果A觉得没问题，就可以将C的修改合并到他的项目中

## 六、SSH免密

> 每次进行push操作时，系统都会进行身份验证（Windows系统下有windows凭据，会记录缓存，所以不需要每次进行身份验证）。如果是其它系统，可以使用SSH进行免密操作。

1. 进入到用户的主目录中：

   ```bash
   $ cd ~
   ```

2. 执行命令，生成一个.ssh的目录：

   ```bash
   $ ssh-keygen -t rsa -C 123456@qq.com
   ```

   keygen --> key generation

   C要大写

   后面的邮箱对应github注册时的邮箱

   三次回车即可

3. 进入目录`C:\Users\dell\.ssh`,找到文件`gitee_rsa.pub`，复制里面的密钥

4. 在GitHub的setting里找到SSH，将复制的密钥添加进去，名称也可以随便起

5. 成功添加密钥之后，就可以进行push操作了

6. 首先，在项目里复制SSH链接

7. 使用命令，给SSH链接添加别名，如`$ git remote add origin_ssh git@xxx`

   ```bash
   $ git remote add <ssh_alias> <sshLink>
   ```

8. 进行push操作，如`$ git push origin_ssh master`

   ```bash
   $ git push <ssh_alias> <branch>
   ```




---

[参考教程](https://www.bilibili.com/video/BV1Zz4y1C7vg?p=1)



