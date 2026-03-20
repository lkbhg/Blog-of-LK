# AirSim 编译和运行时出现Error at startup: Error creating directory, hr=5

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

## 问题描述

这是由于在`AirSim`编译和运行时，在`Document`(即系统`文档`文件夹)下需要提前预制一个`settings.json`作为运行时参数。
但是有时候有些人（比如我）会手动修改这个文件夹的位置，避免C盘被占满。
这个时候就会触发这个bug。

这在展示基本示例时就会出现，类似于这个图片展示的这样：
<img width="1600" height="900" alt="image" src="https://github.com/user-attachments/assets/87cd3701-2b97-4807-bcaf-7aba3429752d" />


完整的报错信息：Error at startup: Error creating directory, hr=5

具体Issue来源为：
[https://github.com/microsoft/AirSim/issues/1150#issue-330970602]
AirSim issue #1150

## 修复方法
简单的修复方法就是把`文档`文件夹改回默认位置就好了。


## 这是本人在GitHub上解决的第一个问题。
