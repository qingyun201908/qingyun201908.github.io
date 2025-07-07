---
title: Trident中台培训
date: 2025-07-07 23:25:13
tags: FF公司
categories:
  - FF公司
---
## Trident中台培训

### 概览
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-5.png)
#### 大数据中台 - 大数据中台位置
正式大数据中台位置
10.90.11.61:7062
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-6.png)
测试大数据中台位置
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-7.png)
#### 操作大数据中台
* 接入数据库
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-9.png)
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-8.png)
* 中台同构
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-10.png)
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-11.png)

<font color=royalblue>中台需要设置主键<\font>

设置分区表达式和分区字符

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-13.png)

分区表达式：如图所示

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-14.png)

<font color=red>IN_ORDER ： 主键字段<\font>

其余固定
<font color=orange>分区字符：一般设置 5 <\font>

<font color=seagreen>设置完成后，点击按钮进行激活<\font>
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-12.png)
* 接入映射 (接入数据到中台)

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-15.png)

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\4fd065720584051d85c2f9274e34c0a.png)
<font color=green>点击清除表达式<\font>
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-17.png)
发布映射
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-18.png)

* 接出映射

数据来源与目标表

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-19.png)

<font color=seagreen>发布映射  是否清空表，选择是
<\font>
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-20.png)

#### 创建任务流
操作位置
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-21.png)
顺序
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-22.png)

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-23.png)

<font color=red>设置定时任务<\font>

![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-25.png)
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-26.png)
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-27.png)



#### 问题
导入导出

#### 方案
* <font color=seagreen>业务确认主键<\font>


#### Navicat 导入导出
导出结构
导出
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-28.png)
导入
![alt text](https:\\cdn.jsdelivr.net\gh\qingyun201908\qingyun201908.github.io@images\images\Trident中台培训\image-29.png)