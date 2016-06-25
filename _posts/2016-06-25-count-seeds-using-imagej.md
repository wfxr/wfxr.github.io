---
layout: post
title: 使用ImageJ实现种子计数
category: 技术
tags: image-processing
description: 介绍了利用ImageJ手动和自动两种统计种子数目的方式
math: true
---

ImageJ是一个由National Institutes of Health
开发的基于Java的开源图像处理软件。如果你从事生物学领域的研究，那你很可能已经用过或者听说过这个软件。

之所以提这个软件，是因为前段时间做油菜转基因实验的女朋友遇到的一个麻烦事：为了研究某个基因对油菜种子产量的影响，她需要统计收获的油菜种子样本的数目。但由于某些原因，种子的饱满程度普遍很差，很难用千粒板（一种用来统计种子数目的平板，将种子抖落到板上，通过人工调整让每粒种子占一个孔，就可以得到一组1000粒的种子）来计数，人工一粒一粒地数工作量又太大，要浪费不少的时间。

当她跟我说了这个情况以后，我的第一反应就是这个问题最佳的解决方案应该不是物理手段，而是计算机的图像处理技术，因为后者的只需要对种子进行拍照，剩下的工作都可以计算机来完成，省时省力。

中间查找资料和试验的过程就不多赘述了，最终的结果是在ImageJ的帮助下，这个问题被轻松解决了。考虑到几乎没有找到介绍用图像处理软件进行高效率种子计数的资料，下面我把这个方法做一个介绍，希望能给遇到类似问题的人提供帮助。
<!-- more -->

## 安装ImageJ

安装ImageJ很简单，不过相比原版的imageJ，我更推荐大家安装集成了大量插件，并且支持自动更新的的发行版[Fiji](http://fiji.sc/)。
进入[Fiji下载页](http://fiji.sc/#download)，根据自己操作系统的类型下载相应的版本，解压之后即可运行。
下文的操作都是基于最新版本的Fiji，但同样也适用于原版的ImageJ。不过人工计数部分要用到一个Cell
Counter插件，如果是原版ImageJ，需要手动去下载安装。

## 前期工作

### 拍摄种子照片

拍摄照片将种子置于纯色背景（如干净的A4白纸），背景和种子颜色要有明显的差异。

为了提高种子计数的精度，种子摆放不要过于密集以免出现覆盖重叠现象。

拍照时镜头尽可能正对，避免照片不同部位比例差异过大。

### 裁剪照片

为了方便后续的批量处理，如果照片边缘超出了纸张或者背景面板的边界，用合适的图片处理软件将其裁去。

## ImageJ自动计数操作流程

### 打开图片

打开ImageJ，在菜单栏里面选择File -> Open，在打开对话框里面选中种子的照片。

### 消除背景

菜单栏选择Process -> Subtract
Background，打开消除背景对话框：

![subtract-background-window]()

在"Rolling ball radius"处填入合适的像素大小；
如果不是亮色背景，取消勾选"Light background";
完成设定后，点击"OK"按钮；

![subtract-background-photo]()

### 转换位图

菜单栏选择Image -> Type -> 8bit，将图片转换成8位格式

### 设置阀值

菜单栏选择Image -> Adjust -> Threshold，打开Threshold对话框：

![threshold-window]()

在右侧下拉框中将类型更改为"Red"；
如果是深色背景，勾选"Dark background"选项；
拖动第二个滚动条，调节阀值的最高值，使其红色区域能刚好覆盖所有种子：
点击"Apply"按钮；

### 切分粘连区域

菜单栏选择Process -> Binary -> Watershed；

### 分析并计数

菜单栏选择Analyze -> Analyze Particles，打开Analyze Particles对话框：

![analyze-particles-window]()

Size参数表示的是尺寸范围，单位是像素的平方，如果不清楚应该设多少，可以先保持默认（0到无穷）；
Circularity参数表示圆度范围，保持默认即可；
Show参数设定是否显示分析出的种子图像，以何种格式显示，一般选择Outlines即可；
下方的复选框根据需要勾选需要输出的内容，可以勾选"Display results"，"Clear
results"，"Summarize"；
完成配置后，点击"OK"按钮；

### 输出结果

**Particles轮廓图**
![particles-outlines-image](/res/img/2016-06-25-count-seeds-using-imagej/particles-outlines-image.png)

**计数结果表格**

表格中的Area列的值即种子的大小，可以据此设定之前Size参数的最小值，以过滤图像中的污点。

![results-table](/res/img/2016-06-25-count-seeds-using-imagej/results-table.png)

**技术结果汇总**
![results-summary](/res/img/2016-06-25-count-seeds-using-imagej/results-summary.png)

## 结论
