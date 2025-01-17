---
title: unity入门(八)-导入人物模型和绑定骨骼动画
date: 2023-04-20 19:19:51
categories:
- Computer Graphics
- Engine
- Unity
tags:
- CG
- Game
- Unity
---

到这里我们基本了解unity和一些重要API的使用了，有素材的话可以试着自己制作2D游戏，但是2D游戏不管是逻辑上还是素材上都要比3D游戏简单很多，例如2D游戏的动画仅仅是快速播放图片，而3D游戏中我们则需要对动画进行深入了解，知道一些基本概念。这一篇博客会指导你将任何fbx模型导入到Unity中，并用真正的动画系统让这个模型动起来，然后就能继续制作3D游戏了。

<!--more-->

# 导入模型

## 模型简介

3D模型通常是建模师使用3D Max，Blender等建模软件制作的游戏资产，从编程角度来说就是一系列点和面坐标、纹理构成的3D对象，一般有以下格式：

* fbx是一种比较通用的格式，可以在多个3D软件之间进行导入和导出，支持多种功能，如动画、材质、光照、纹理等。
* obj格式是一种比较简单的格式，只包含模型的顶点和面信息，不支持动画和材质等高级功能。
* 3ds格式是3D Studio Max软件的专用格式，支持多种功能，如动画、材质、光照等。
* PMX是一种用于MikuMikuDance（MMD）的模型文件格式，它是MMD中的一种新型模型格式，通常包含模型的顶点、面、纹理、骨骼、材质、动画等信息。

Unity支持的模型格式是fbx，因此我们找到的模型必须要转换为fbx格式才能导入到unity中，为此后面我们会介绍一个Blender的插件及其使用方法。

Fbx文件中包含的信息主要包括以下内容：

- **网格 Mesh :** 表示 3D 物体的 形状 ;
- **材质 Material :** 表示 3D 物体的 表面特性 ;
- **纹理贴图 Texture :** 定义 3D 物体 表面的 像素颜色 , 一般是一张图片 ;

结合我们之前学习图形学的经验可以知道，这里Material定义了物体表面的反射率、颜色、透明度等信息，纹理则是描述物体表面信息的另一个属性，例如贴图等。Unity中的流程是，将纹理应用到材质上，然后材质应用到模型上，最后还可以编写Unity Shader添加更多效果，在另一个系列的博客《Unity和Shader入门》中可以查看这部分内容。

## 模型素材和动画的获取

以下网站是我们后面获取素材的主要途径：

* Adobe旗下的网站[Maximo](https://www.mixamo.com/#/)提供了一系列免费的白模(没有提供材质的模型)，但是这个网站最主要的还是提供了大量的动画素材，我们后面的动画都在这个网站上获取；
* [free3d.com](free3d.com)提供了很多带有材质的完整模型；
* [模之屋](https://www.aplaybox.com/)中有很多MMD制作需要的二次元模型，但是都是pmx格式，需要进一步处理。
* VRoid Studio是一款免费的3D角色建模软件，可以用于创建自己的3D角色模型，类似捏脸一样可以快速导出；
* Adobe Fuse是由Adobe公司开发的3D人物制作工具，可以用于创建自己的3D角色模型，也是捏脸。

从上面的网站基本可以满足我们需要了，关于动画我们后续可能还会介绍使用IK进行快速制作。

## pmx到fbx格式的预处理

现在我们在模之屋上找一个角色模型并下载解压，得到文件夹中有一个pmx模型文件，一个Texture纹理文件夹，还可能有其他文件，不过我们不关心，只需要这两个就能使用了。其中pmx文件就是我们的白模，Teture文件夹提供了模型对应的纹理。

现在我们在官网安装好Blender，最好是2.93版本，否则可能不支持插件。

![image-20230420201308939](C:/Users/16677/AppData/Roaming/Typora/typora-user-images/image-20230420201308939.png)

安装完成后到[GitHub](https://github.com/absolute-quantum/cats-blender-plugin)上点击上图中的蓝色字体下载我们要使用的Cats插件压缩包。Cats插件支持MMD格式的模型导入，可以将MMD格式的模型导入到Blender中进行编辑和优化，然后导出为fbx格式的模型文件，还提供了丰富的工具和功能，如自动绑定骨骼、自动修复模型、自动优化模型等，可以大大提高模型的制作效率和质量。

得到的Zip文件不要解压，在Blender中打开Edit/Preferences/Add-ons，点击右上角的Install，找到刚才下载的压缩包，点击Install Add-on即可安装。

![](unity8/2.png)

然后到Blender右侧的侧边栏中找到小箭头(上图红色圆圈中的)并点击打开新的侧边栏，看到如下图所示就是安装成功了。

![](unity8/3.png)

接下来我们点击CATS，打开插件界面，点击Import Model，然后找到我们刚才下载的pmx文件，等待导入，成功后角色就在场景中了，但是我们发现模型有些问题，既没有纹理，模型节点也不是按照英文命名。

那么首先我们点击Misc中的Shadeless，使用卡通渲染，发现已经有颜色了。

接着我们点击Fix Model，这个功能主要是由于MMD格式的模型与Blender的模型格式不同，可能会出现一些问题，如骨骼不对齐、材质丢失等。使用Cats插件导入MMD格式的模型后，可以使用Fix Model功能来修复这些问题，以便更好地在Blender中编辑和导出模型。这里使用这个功能可以帮助我们解决命名问题，并且还可以将多余的骨骼删除，将多个节点的Mesh合并到一个名为Body的Mesh等，具体我也不是很清楚，只需要知道Fix Model可以帮助优化模型即可，其实前面的卡通渲染也可以通过这里的Fix Model完成。

![](unity8/4.png)

点击后等待运行，发现角色的纹理也被正确识别上去了，现在我们得到了一个包含正确骨骼，材质，纹理信息的模型，点击Export Model，命名，然后点击Export FBX，就得到了FBX模型。

## 将FBX模型和贴图导入Unity

将FBX文件和Texture文件拷贝到Unity中(这里我们使用的是3D URP模板，因为后面的Shader会使用URP)，你可以创建一个名为Models的文件夹来存放这些模型文件，如果没有Texture文件夹，那么模型就是白模。

接着选中我们的FBX模型，点击rig属性，修改Animation Type为Humannoid，这意味着我们选择将导入模型的动画类型设置为人形，这可以保证模型的骨骼是人体的五个骨骼部分，你可以点开下图中的最右侧绿色小人，然后选择Configure Avatar来查看，保证骨骼是正确绑定的。

![](unity8/5.png)

![](unity8/7.png)

拷贝后模型自动识别了贴图，在预览中可以查看。但是渲染方式是不正确的，因为从图形学来说，贴图归贴图，这里不是什么高级的法线贴图，渲染方式还是要看Shader和材质，我们贴图放到Texture中给FBX找，但是材质现在就不一样，它已经在FBX中了，了解Unity Shader的同学应该知道Shader要应用在材质上才能生效。而Unity又不是建模软件，不能对FBX进行修改，也就意味着其中的材质我们也不能修改。

因此我们要把材质导出来，点击模型文件，选择Materials属性，将Loaction设置为Use External Materials(Legacy)并Apply，此时材质会自动放到Materials文件夹中；或者点击Extract Materials，在同级文件夹创建一个Material文件夹，把导出的材质存放到这里。（这里前一种方式可能会出现头发全黑的问题，应该是材质命名的问题，建议使用第二种方式修改）

到这里我们导入模型已经基本完成了，模型，骨骼，纹理，材质都已经导入，将模型创建成游戏对象，下面我们来说一说怎么修改材质才能达到希望的渲染效果。

# 设置渲染效果

说到渲染，Unity Shader肯定是不可避免的，不过怎么写一个Shader在本系列中不重要，感兴趣可以看另外一个系列《Unity和Shader入门》。我们这里使用[ColinLeung](https://github.com/ColinLeung-NiloCat)大佬编写好的Shader即可，GitHub地址是https://github.com/ColinLeung-NiloCat/UnityURPToonLitShaderExample，直接下载压缩包并解压，然后在Unity中创建Shader文件夹，把得到的文件全部拖进去。

然后回到材质，我们进入Material文件夹，全选，将它们的Shader都设置为刚刚导入进来的SimpleURPToonLitExample，保存；然后由于这种渲染需要对面部进行特殊处理(不处理的话面部阴影会被头发遮盖，导致脸黑)，所以找到面部的材质，将右边Is Face勾选，此时看到场景中的人物已经是卡通渲染的了，脸也亮起来了。下面第一张图是没有勾选的，第二张图已经勾选。

![](unity8/8.png)

![](unity8/9.png)

这样我们就实现了卡通实时关照了，值得一提的是，这个Shader项目也很值得学习，涉及各个方面的内容，之后在另一个系列中也会详细介绍。

![](unity8/10.png)

# 动画系统

## 3D动画系统简介

Unity中的动画以骨骼为核心，骨骼在程序中定义为一个结构体，具有一个矩阵M表示当前骨骼位置，一个字符串表示骨骼名称，还有一个指向父骨骼的指针，这种骨骼定义将模型分为不同的部分，而且骨骼之间能相互带动，通过图形学基础的学习我们知道矩阵M的连乘就可以表示子骨骼的位置变化。

通过骨骼动画，动画师只需要做出动画的关键帧，然后Unity会根据这部分内容进行插值，得到完整的动画效果，大大减小了动画师的工作量，而且由于骨骼之间的联动动画也比较自然。

而模型是由很多三角性面组成的，在有了骨骼之后，就需要进行蒙皮，将模型绑定到骨骼上，绑定好之后组成模型的这些三角型面就可以随着骨骼进行运动了，三角型面的位移也是通过骨骼来获取。

除此外，还有两种程序向动画，FK(Forward Kinematics)和IK(Inverse Kinematics)，即正向运动学和反向运动学，分别表示以父骨骼为首进行的动画和以子骨骼为首进行的动画。这种技术是完成地形和角色动画交互的关键，例如防止角色在斜坡上不会自动纠正动作，或者攀爬中和地形进行交互。

## 绑定动画骨骼

3D动画和2D动画的最大区别就是骨骼动画，我们要让模型动起来就要先确定模型的骨骼Avatar。

我们之前使用2D的动画系统制作动画效果，使用Animation Clip作为素材，添加到Blend Tree中管理，然后使用Animator中的Animation Controlller进行播放控制，其实在3D动画中也是类似的，只是我们制作Animation Clip不再使用图片快速播放，而是需要将绑定到骨骼上。

听起来很复杂，但是我们这一部分已经通过CATS插件完成了，角色的骨骼正确绑定到模型上，而将骨骼和动画绑定就只需要将这个骨骼赋值给Animator组件的Avatar属性就完成了，你可能还记得我们当时并未使用这个属性，原因就是2D动画并不需要绑定骨骼。

![](unity8/11.png)

可以看到我们创建的模型已经自动添加了Animator组件并绑定了骨骼。

其他操作则完全和2D动画类似，我们依然要设计状态机来判断动画的播放时机，使用Animator的一系列API来进行脚本和动画的同步。

## 制作3D Animation Clip

现在来说一下怎么获取动画素材，在前面介绍的网站Maximo上提供的动画需要配合模型进行导出，也就是说我们就算下载了动画也还是得到fbx文件，只不过fbx文件中包含了动画Clip，那应该怎么提取呢？我们找到想要的动画以后(这里是一个Idle动画)，点击Download，看到以下界面：

![](unity8/12.png)

Format表示下载格式，我们当然选择fbx(这里还提供了fbx for unity，不清楚有无差别，暂时没发现问题)；Skin表示是否需要连同角色一起下载，这里就是Without Skin；Frame per Second表示动画的帧率，帧率越高动画越精确，但是Unity会使用插值算法处理动画，所以不用选择太高；最后KeyFrame Reduction表示是否要删除变化幅度太小的动画关键帧，从而节省资源，这里也暂时不用。

![](unity8/13.png)

将fbx文件导入Unity后，展开，看到需要的动画已经被识别成切片了，它的图标和我们之前2D学习中看到的一致。接着按照前面相同的操作处理这个fbx，修改为人形并Apply，打开Avatar查看骨骼是否正确。然后就可以使用这个动画了，将切片拖动到游戏对象上，Unity自动创建了一个和游戏对象同名的Animation Controller，并且赋值给Animator组件，还将这个Clip直接添加到Animator的动画状态机界面中了。

此时我们重新选择这个Idle动画，然后点击Edit，下面是一系列属性，勾选Loop Time，就能让这个动画循环播放，然后拉到最下面Apply，运行游戏，此时发现人物已经动起来了。

然后你应该知道怎么做了，使用Animation Controller对他们进行管理。

还要注意的是这里的Animation Clip因为是在fbx文件中的，所以是只读文件，要修改的话需要单独导出来，复制一份即可，此时重新添加给角色，就可以进行修改了。

![](unity8/14.png)

## 可能存在的问题

以上描述也许不会非常相近，你可以查看下面三篇博客学习：

* [【游戏开发实战】下载原神模型，PMX转FBX，导入到Unity中，卡通渲染，绑定人形动画（附Demo工程）](https://blog.csdn.net/linxinfa/article/details/121370565)

* [【Unity3D】使用 FBX 格式的外部模型 ② ( FBX 模型与默认 3D 模型的区别 | FBX 模型贴图查找路径 | FBX 模型可设置多个材质 )](https://blog.csdn.net/shulianghan/article/details/127774380)
* [Unity为人物模型 添加动效Animator](https://blog.csdn.net/newchenxf/article/details/123276198)

此外，如果你尝试使用了移动动画的话，你会发现角色身上的布料等物品都没有移动过，非常僵硬，这是因为虽然这些地方也绑定了骨骼，但是我们并没有规定它们如何移动，刚才导入的动画也只是控制了角色本身的骨骼。

因此在后面的博客中我们将用一个免费的Unity插件来实现角色的布料模拟，并防止布料在角色身上穿模。你可以通过这个插件来了解如何给Unity编写插件，以及如何对提供的插件进行修改，为自己编写插件积累经验，从而完成某些自动化工作。
