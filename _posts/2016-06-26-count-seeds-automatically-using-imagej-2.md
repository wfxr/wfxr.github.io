---
layout: post
title: 使用ImageJ实现种子的自动计数（二）
category: 技术
tags: Image-Processing Macro
description: 介绍了利用ImageJ实现自动统计种子数目的方法
math: false
---

通过[上一篇文章](http://wenxuan.info/blog/2016/06/25/count-seeds-automatically-using-imagej-1.html)的介绍，你应该可以熟练的使用ImageJ对种子的照片进行分析计数了。不过如果要处理大量的图片，这种方式还是不够快捷。

原因就是虽然我们已经把计数的环节交给了程序去处理，但是操作流程却并没有实现自动化。每次我们都要手动重复这些相同的步骤而这恰恰是计算机最擅长做的。

下面就介绍如何通过使用宏自动化我们的操作流程。

<!-- more -->

自动化
======

编写宏
------

对于每一张照片，我们都要先消除背景，转换为8位图，然后运行Threshold，这一系列的操作是固定的，分别对应下面的宏命令：

```
run("Subtract Background...", "rolling=20 light");
run("8-bit");
run("Threshold...");
```

在调整完Threshold阀值之后，接下来要执行区域分割和分析计数操作，分别对应下面的宏命令：

```
run("Watershed");
run("Analyze Particles...", "size=100-Infinity show=Outlines display clear summarize");
```

可以看出，虽然看起来我们执行了一系列非常复杂的操作，但实际上对应的程序命令只有这么几条，接下来，我们只要把这些命令写到一个宏文件里面让ImageJ执行加载，接下来就可以用非常快捷的方式完成这些操作了。

完整的宏文件：

```
macro "Seeds Counting Preprocssing..." {
	run("Subtract Background...", "rolling=20 light");
	run("8-bit");
	run("Threshold...");
}

macro "Seeds Counting" {
	run("Watershed");
	run("Analyze Particles...", "size=100-Infinity show=Outlines display clear summarize");
}
```

将以上内容保存到一个文本文档即可。

加载宏
------

* 菜单栏Plugins -> Macros -> Install... -> 选择上一步骤中编辑好的宏文件 -> 点击"OK"按钮

* 点击Plugins -> Macros，会发现Macros的子菜单下会多出两个命令："Seeds Counting Preprocssing" 和"Seeds Counting"，说明宏已经被正确加载了;

使用宏
------

* 打开一张种子的照片；

* 点击Plugins -> Macros -> Seeds Counting Preprocssing，ImageJ会帮我们完成图像的预处理，直接显示Threshold对话框，调节好参数以后，点击"Apply"即可；

* 点击Plugins -> Macros -> Seeds
Counting，ImageJ已经完成了区域分割和分析计数的操作，并显示了轮廓图，计数表格和汇总表格，我们已经得到了和之前手动操作一样的结果。

至此，我们已经的流程已经简化为，打开图片 -> 点击Seeds Counting Preprocssing ->
设置Threshold参数 -> 点击Seeds Counting。

美中不足的是，每次我们都要到去Plugins菜单的Macros子菜单下去寻找我们的宏命令，这一过程能不能简化呢？答案是肯定的。

改进宏
------

* 我们可以给两个宏命令分别设定一个快捷键，这样就免去了找宏命令和鼠标点击的工作。在之前的基础上稍作修改即可：

```
macro "Seeds Counting Preprocssing [f1]" {
	run("Subtract Background...", "rolling=20 light");
	run("8-bit");
	run("Threshold...");
}

macro "Seeds Counting [f2]" {
	run("Watershed");
	run("Analyze Particles...", "size=100-Infinity show=Outlines display clear summarize");
}
```

*修改完成后，不要忘了重新加载一次*

现在操作流程更简单了，打开图片 -> 按下F1 -> 配置Threshold参数 -> 按下F2；

* 如果能保证照片有较好的质量，那么可以免去配置Threshold参数的过程，将两个宏合成一个：

```
macro "Seeds Counting [f3]" {
	run("Subtract Background...", "rolling=20 light");
	run("8-bit");
    setAutoThreshold("Default");
    setOption("BlackBackground", false);
    run("Convert to Mask");
	run("Watershed");
	run("Analyze Particles...", "size=100-Infinity show=Outlines display clear summarize");
}
```

如此以来，要对一张种子的图片计数，仅仅需要打开图片然后按下F3即可；

* 如果图片数量相当庞大，还可以编写脚本自动处理每一张图片。原理很简单，用脚本遍历指定文件夹下的每一张图片，对其调用前面编写好的ImageJ宏命令。

演示
====

使用手动调节Threshold参数的宏
-----------------------------

![demo1](/res/img/2016-06-25-count-seeds-using-imagej/demo1.gif)

使用自动调节Threshold参数的宏
-----------------------------

![demo2](/res/img/2016-06-25-count-seeds-using-imagej/demo2.gif)

