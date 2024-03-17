---
title: 图形学知识合集
date: 2023-07-31 16:49:35
categories:
- Computer Graphics
tags:
- CG
---

经过大约一年时间的学习，深感图形学内容的丰富，加上接触了游戏引擎，从底层API到上层如unity shader都有了一定认识，但是由于之前没有系统整理，现在决定从头梳理一遍知识目录，作为日后参考使用，保持更新或修改。

<!--more-->

# 理论基础

基础部分以了解底层原理为主，具体实现参考TinyRenderer，该项目用C++实现了从模型读取到输出渲染结果的软光栅化渲染管线，对理解原理很有帮助。

## 数学基础

主要是一些常用的计算方法，向量矩阵加减乘之类的就不说了。

| 内容                       | 应用                                                         | 链接                                     |
| -------------------------- | :----------------------------------------------------------- | :--------------------------------------- |
| 三角形重心坐标             | 软光栅化，光线追踪，判断点是否在三角形内部，三角形内部坐标插值 | https://zhuanlan.zhihu.com/p/58199366    |
| 傅里叶变换                 | 反走样                                                       |                                          |
| 线性变换/仿射变换/齐次坐标 | MVP变换                                                      | fundamentals of computer graphics 第六章 |
| SVD奇异值分解              | 将线性变换矩阵分解为平移、缩放、旋转的组合矩阵形式           | fundamentals of computer graphics 第六章 |
| Peath分解                  | 将旋转分解为错切的组合形式                                   | fundamentals of computer graphics 第六章 |
| TBN矩阵                    | 法线纹理                                                     |                                          |
| 法向量平移                 | MVP变换                                                      |                                          |

## 渲染管线

| 分类         | 简述                                                         | 链接                                              |
| ------------ | :----------------------------------------------------------- | :------------------------------------------------ |
| 概念渲染管线 | 出自实时渲染第四版，将渲染管线抽象总结为概念上的渲染管线，可以称为大多数管线的做法 | https://ankiima.github.io/2023/04/11/uni-shader1/ |

## 光栅化

在现代管线中只需要绘制三角形就可以了，直线的绘制则是最基本的知识，都需要熟练掌握，最好能手写。图元的绘制也被称为光栅化。

### 直线

Bresenham算法：迭代绘制，不计算浮点数提高了运行效率。

| 常见形式               | 链接                                                         |
| ---------------------- | :----------------------------------------------------------- |
| 推导决策量：$P_k$      | https://blog.csdn.net/qq_41883085/article/details/102706471  |
| 直接计算(不使用浮点数) | https://github.com/ssloy/tinyrenderer/wiki/Lesson-1:-Bresenham%E2%80%99s-Line-Drawing-Algorithm |

### 三角形

主要是新旧两种做法：

| 方法           | 描述                                                       | 链接                                                         |
| -------------- | :--------------------------------------------------------- | :----------------------------------------------------------- |
| (旧)扫描线法   | 三角形分上下两部分，根据三角形边界分别填充它们             | https://github.com/ssloy/tinyrenderer/wiki/Lesson-2:-Triangle-rasterization-and-back-face-culling |
| (新)像素迭代法 | 确定包围三角形的正方形，判断每个像素是否在三角形内进行绘制 | https://github.com/ssloy/tinyrenderer/wiki/Lesson-2:-Triangle-rasterization-and-back-face-culling |

### 可见性问题

| 方法/问题  | 简述                                                         | 链接                                                         |
| ---------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| 画家算法   | 排序所有面，先绘制在后面的，然后绘制在前面的面以遮挡后面的面，开销大，且不保证得到完全正确的顺序 | https://zh.wikipedia.org/wiki/%E7%94%BB%E5%AE%B6%E7%AE%97%E6%B3%95 |
| Z-buffer   | 用缓存区存储深度信息，绘制在前面，也就是深度值排在前面的面，需要对单个像素进行插值 | https://github.com/ssloy/tinyrenderer/wiki/Lesson-3:-Hidden-faces-removal-(z-buffer) |
| z-fighting | z-buffer深度值不精确比较带来的像素抖动                       | https://zhuanlan.zhihu.com/p/78769570                        |

### 背面剔除

下面分类的方法没有任何依据，我只是想说明可以用不同方法定义背面。

|       方法       | 简述                                                      | 链接                                                         |
| :--------------: | :-------------------------------------------------------- | :----------------------------------------------------------- |
| 根据光照方向判断 | 法线和光线方向夹角小于90度认为是正面，否则剔除            | https://github.com/ssloy/tinyrenderer/wiki/Lesson-2:-Triangle-rasterization-and-back-face-culling |
|  顺/逆时针判断   | 根据面的前后两面中顶点的顺/逆时针确定前面和背面，如OpenGL | https://blog.csdn.net/wangdingqiaoit/article/details/52267314 |

### 抗锯齿

由于像素是离散的，我们绘制的时候如果像素太少，导致采样的频率不高，就会出现信号走样的问题，直观来说就是锯齿。为了在不改变硬件条件下修改这些锯齿表现，提出一系列方法改进。

| 方法         | 简介                                                         | 链接 |
| ------------ | :----------------------------------------------------------- | ---- |
| MSAA(超采样) | 将一个片元划分为多个进行光栅化，每个片元平均自身划分采样的结果，从而增大采样频率 |      |
| FXAA         |                                                              |      |
| TAA          |                                                              |      |

## MVP变换

## 着色

### 着色方式

模型文件一般会给出每个面的法向量，我们着色要对每个像素确定一个光照的值，所以根据着色计算频率可以分为不同的着色方式。

| 方式            | 简述                                                         |
| --------------- | :----------------------------------------------------------- |
| Flat Shading    | 每个面计算一次，作为面内所有像素的着色，是逐个面的着色       |
| Gouraud Shading | 每个顶点计算一次，顶点法向量通过对顶点连接的面计算得到(可以是简单平均)，面内像素对三个顶点的颜色线性插值得到像素的颜色，实际上是逐顶点的着色 |
| Phong Shading   | 每个像素计算一次，像素对应的法向量由顶点的法向量插值得到，逐像素的着色 |

### 光照模型

| 模型                | 简述                                                         | 链接                                                   |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------ |
| Phong               | 最经典的经验光照模型，不满足能量守恒，将物体表面的光分为Ambient，Diffuse，Specular三种分别计算后相加 |                                                        |
| Blinn-Phong         | 对Phong进行了一点改进，不在比较反射光和观察视角的夹角作为镜面光，而是计算观察视角和光照方向的半程向量，同法线比较作为镜面光 |                                                        |
| Cook-Torrance / PBR | PBR光照模型，定义了满足能量守恒的BRDF函数，计算光照到达物体之后被折射和被反射的部分 | https://learnopengl-cn.github.io/07%20PBR/01%20Theory/ |

### 阴影

#### 软阴影

| 方法 | 简述 | 链接 |
| ---- | ---- | ---- |
|      |      |      |

#### 硬阴影

| 方法 | 简述 | 链接 |
| ---- | ---- | ---- |
|      |      |      |

## 纹理原理

### 纹理映射

每个顶点拥有一个纹理坐标(u,v)，通过纹理坐标找到纹理图中的颜色，就可以作为顶点颜色。但光栅化的时候为了得到每个像素的颜色，自然不能逐像素地添加纹理坐标，一个三角形片元只有三个顶点的三个纹理坐标，通常有两种方法获得逐像素的颜色。

| 方法         | 简介                                                         | 链接                                                     |
| ------------ | :----------------------------------------------------------- | -------------------------------------------------------- |
| 纹理坐标插值 | 通过重心坐标对纹理坐标进行插值，得到逐像素的纹理坐标，再进行纹理映射，可以配合纹理图得到丰富的细节 |                                                          |
| 顶点颜色插值 | 对三个顶点的颜色进行插值，得到逐像素的平滑颜色，细节不足，但是足够平滑 | https://ankiima.github.io/2023/08/01/tinyRenderer2/#more |
| 纹理放大     | 由于纹理图片分辨率低，一个uv坐标放大到纹理分辨率后是非整数值，夹在几个像素点之间，需要对这几个像素点的颜色进行插值。 |                                                          |
| 纹理缩小     | 由于纹理图片分辨率过大，一个uv坐标放大到纹理分辨率后同时对应好几个像素点，而不是夹在几个点之间，如果只采用查询点的像素值会导致抖动，需要用Mipmap让范围查询代替点查询。 |                                                          |

## 几何

### 曲线

#### 贝塞尔曲线

| 分类           | 链接                                         |
| -------------- | :------------------------------------------- |
| 二次贝塞尔曲线 | https://ankiima.github.io/2022/09/07/games4/ |
| 分段贝塞尔曲线 |                                              |
|                |                                              |

### 曲面

#### Subdivision

将曲面分成更多的面，提升模型的精度。

| 分类                    | 简述                         | 文章                                                         |
| ----------------------- | :--------------------------- | :----------------------------------------------------------- |
| Loop Subvision          | 只能用于三角形的曲面细分方法 | https://www.microsoft.com/en-us/research/wp-content/uploads/2016/02/thesis-10.pdf |
| Catmull—Clark Subvision | 适应任意形状的曲面细分方法   | https://www.cs.jhu.edu/~cohen/Seminar/cc.pdf                 |

#### Simplification

将曲面合并成更少的面，减少性能开销。

| 分类                                    | 简述 |      |
| --------------------------------------- | ---- | ---- |
| Edge Collapsing & Quadric Error Metrics |      |      |

#### Regularization

将不规则分布的曲面规则化，不改变模型面数的情况下提升表现效果。

## 光线追踪

不同于光栅化的着色方法，基本按照光学原理在场景中计算光线的弹射，模拟全局光照效果。解决了光栅化无法一次完成阴影计算，以及无法模拟全局光照的问题。

| 方法             | 简述                                     | 链接                                                         |
| ---------------- | :--------------------------------------- | :----------------------------------------------------------- |
| 光线追踪技术合集 | 关于光线追踪的技术论文合集               | https://github.com/LouiValley/RayTracing-Tech#industry-contributions |
| 加速结构         | 加快光线追踪中光线求交的一种区域划分方法 |                                                              |

## 路径追踪

光线追踪将光照模拟为折射和反射，折射后通过判断此时是否能达到光源，也就是是否产生镜面光作为收敛条件，忽略了漫反射的存在。路径追踪和光线追踪的方法类似，但是模拟了多条光线反射的情况，不只是一次光线追踪，所以能达到更好的效果。

# API

## OpenGL

## Vulkan

## DirectX

# Shader Language

## C for Graphics

## GLSL

## HLSL

# Unity Shader

## 基础

### 概述

| 名称                       | 简述                                                         | 链接                                              |
| -------------------------- | :----------------------------------------------------------- | :------------------------------------------------ |
| 概念流水线                 | 渲染管线的抽象总结，分成应用、几何、光栅化三个阶段           | https://ankiima.github.io/2023/04/11/uni-shader1/ |
| Shader                     | 渲染流水线中高度可编程的阶段                                 | https://ankiima.github.io/2023/04/11/uni-shader1/ |
| 渲染状态                   | 定义场景中的网格怎么渲染，绑定Shader，材质，光源等内容，例如混合，深度缓冲，背面裁剪等 |                                                   |
| Unity Shader               | 既能同时编写顶点和片元着色器，又能设置渲染状态的高级编辑工具，采用ShaderLab语言编写 | 《Unity Shader入门精要》 第一章                   |
| Unity Shader基本结构和语法 | 一个Unity Shader文件使用Properties语义作为输入，包含多个SubShader，选择第一个能运行的SubShader。一个SubShader有多个Pass，程序会从上到下运行这些Pass。 | https://ankiima.github.io/2023/04/11/uni-shader2/ |

## 进阶

| 名称          | 简述                                       | 链接                                                         |
| ------------- | :----------------------------------------- | :----------------------------------------------------------- |
| 基础Phong光照 | Phong光照以及Blinn-Phong光照的实现         | https://ankiima.github.io/2023/04/11/uni-shader3/            |
| 纹理          | 实现颜色纹理、法线纹理、渐变纹理、遮罩纹理 | https://ankiima.github.io/2023/04/11/uni-shader3/ andhttps://ankiima.github.io/2023/04/11/uni-shader4/ |
| 透明与混合    | 实现透明和带混合的透明效果                 | https://ankiima.github.io/2023/04/11/uni-shader4/            |



## 高级

### 高级纹理

| 名称       | 简述                                              | 链接                                              |
| ---------- | :------------------------------------------------ | :------------------------------------------------ |
| 立方体纹理 | 创建和应用天空盒子CubeMap，实现对立方体纹理的采样 | https://ankiima.github.io/2023/04/11/uni-shader5/ |
| 渲染纹理   | 将场景渲染到一张纹理中保存起来                    | https://ankiima.github.io/2023/04/11/uni-shader5/ |
| 程序纹理   | 使用脚本生成纹理                                  | https://ankiima.github.io/2023/04/11/uni-shader5/ |
