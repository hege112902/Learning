@[TOC]
- [二极管](#二极管)
    - [原理](#原理)
    - [分类](#分类)
    - [特性](#特性)
    - [常用应用电路](#常用应用电路)
        - [学习资料 ](#学习资料-)
- [GPU](#gpu)
    - [GPU \& CPU](#gpu--cpu)
    - [不是能设计CPU就能很快能设计GPU：](#不是能设计cpu就能很快能设计gpu)

# 二极管

### 原理

![示例图片](/Hardware/Figures/diode.jpg)

- 无外加电压时：<br>
p-n结两边载流子浓度差 -> 扩散运动 -> 扩散电流 -> 自建电场 -> 漂移运动 -> 漂移电流 <br>
扩散电流 & 漂移电流: 相等，处于电平衡状态。 <br>

- 外加正向电压(P->N)时：<br>
外界电场 & 自建电场: 互相抑消作用 -> 载流子的扩散电流增加引起了正向电流（也就是导电的原因）; <br>

- 外加反向电压(N->P)时：<br>
外界电场 & 自建电场: 进一步加强 -> 在一定反向电压范围中, 与反向偏置电压值无关的反向饱和电流I0（这也就是不导电的原因）。

当外加的反向电压高到一定程度时，p-n结空间电荷层中的电场强度达到临界值产生载流子的倍增过程，产生大量电子空穴对，产生了数值很大的反向击穿电流，称为二极管的击穿现象。


### 分类

### 特性

### 常用应用电路


##### 学习资料 <br>
- [二极管原理及特性-二极管不同分类与整流电路详细解析-KIA MOS管](http://www.kiaic.com/article/detail/1809.html)
- [全面认识二极管，一篇文章就够了](https://blog.csdn.net/weixin_42328389/article/details/124585477)
- [深入解读计算机：从二极管到CPU、GPU和大规模集群，每一步都做了什么？](https://www.youtube.com/watch?v=0rQAN9IPNr4)



# GPU

### GPU & CPU

gpu跟cpu比较：
1. gpu的核比cpu多很多。特别特别多；
2. gpu的核比cpu的简单很多。即cpu的核能执行更复杂的计算任务；
3. gpu的显存相当于cpu的L3缓存，且显存比CPU的L3缓存大很多！因为GPU需要更大量的数据的吞吐量！

### 不是能设计CPU就能很快能设计GPU：
- GPU的设计有很多技术壁垒，
    - NVIDIA 有很多专利；
    - NVIDIA 还有配套的软件，比如CUDA；
    - NVIDIA 进而有了成熟的生态
