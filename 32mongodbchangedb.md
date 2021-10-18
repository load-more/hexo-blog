---
title: 32 【踩坑】MongoDB更改数据库位置（Windows）
date: 2021-03-10 20:54:20
tags: [MongoDB, 踩坑]
categories: MongoDB
---

把 MongoDB 装在了 C 盘，这时默认数据存储在 C 盘的 data 下，这时要把存储路径换到 E 盘，遇到了坑。

<!-- more -->

- 在目标路径创建文件夹 data 、文件夹 log 和配置文件 mongod.cfg（我的目标路径为：E:\mongodb）

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210310210445.png)

- 在 data 里创建一个子文件夹 db，用于存放数据库

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210310210534.png)

- 在 log 里创建日志文件 mongod.log

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210310210600.png)

- 在配置文件 mongod.cfg 中输入以下命令：

  ```
  logpath=E:\mongodb\log\mongod.log
  dbpath=E:\mongodb\data\db
  ```

  指定 日志路径 和 数据库路径

- 接下来 window + R 打开运行窗口，输入 regedit ，打开注册表

  找到以下路径的文件：

  `HKEYLOCALMACHINE\SYSTEM\CurrentControlSet\services\MongoDB`

  修改文件的 imagePath

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210310211154.png)

  按照对应的路径修改 imagePath 的内容：

```
"C:\Program Files\MongoDB\Server\4.4\bin\mongod.exe" --config "E:\mongodb\mongod.cfg" --logpath="E:\mongodb\log\mongod.log" --service
```

​		修改完之后，就已经完成了对**数据库路径**和**日志文件路径**的重定向

- 打开 cmd ，输入 `services.msc`，找到 MongoDB

  ![](https://gitee.com/gainmore/imglib/raw/master/img/20210310211553.png)

  可以发现，此时已经完成了修改。之后连接本地数据库之后，就可以正常使用了。

  *完成所有配置之后，MongDB会开机自动启动，不用再使用 mongod 启动数据库了。直接连接即可。*

---

{% link  参考资料, https://blog.csdn.net/weixin_33889245/article/details/85712811?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control&dist_request_id=1328626.11798.16153778247318435&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control %}