---
title: TextMeshPro
date: 2024-05-27 17:50:37
tags:
- Unity
- Unity Plugins
categories:
- Engine
- Unity
- Plugins
---

最近项目使用Unity的TextMeshPro插件实现图文混排，借这个机会记录一下该插件的使用。TextMesh是原本Unity使用的文本配置组件，但是该组件功能有限，使用位图呈现文字导致放大后字体模糊，性能消耗也大。于是有人制作了该插件弥补这些缺点，并在2017年被收录进Unity引擎。

<!--more-->

# 开始使用

TextMeshPro的核心是使用纹理来表现文本，插件根据Font文件（字体）生成一个Texture Atlas，通过有符号距离场进行渲染，这里不在展开说明渲染方法。并且注意，该插件有两种，*TextMeshPro*配合*MeshRenderer*进行渲染，适用于世界坐标系中的物体；*TextMeshProUGUI*配合*CanvasRenderer*进行渲染，适用于屏幕空间的物体。除了配合的插件不同，两种类型的使用方法完全相同。

![](D:\gzq\blog\ANKIIMA.github.io\source\_posts\TextMeshPro\1.png)

新建一个物体添加该组件，这里组件名称中的UI代表的就是UGUI配合Canvas进行渲染的类型。下面表格说明了各个属性的使用：

| 属性            | 说明                                           |
| --------------- | ---------------------------------------------- |
| Text Input      | 文本编辑                                       |
| Text Style      | 字体风格（一些预设形式）                       |
| Font Asset      | 字体资产                                       |
| Material Preset | 材质预设                                       |
| Font Style      | 字体风格（斜体、粗体、大小写等）               |
| FontSize        | 字体大小                                       |
| Auto Size       | 自动设置字体大小（不超过边界）                 |
| Vertex Color    | 字体颜色                                       |
| Color Gradient  | 颜色过渡                                       |
| Override Tags   | 颜色设置是否重载                               |
| Spacing Options | 行距、间距设置                                 |
| Alignment       | 对齐方式                                       |
| Wrapping        | 平铺模式，Enable表示水平超过文本区域时自动换行 |
| Overflow        | 溢出模式                                       |

# Rich Text

有时我们希望文本中部分字词表现不同，可以使用富文本标记来实现。和HTML语言一样，需要特殊显示的文本采用<>和</>进行标记，例如在文本中单独标记一段斜体，或设置不同颜色：

```
This is a <i>good</i> game.
Have a <color=red>nice</color> day.
Have a <color=#ff0000ff>nice</color> day.
```

![](D:\gzq\blog\ANKIIMA.github.io\source\_posts\TextMeshPro\2.png)

<line-indent=15%>表示行首缩进15%的文本区域。此外还有很多文本标记，最有用的功能是实现超链接和配合Sprite Asset实现图文混排，[文档](http://digitalnativestudios.com/textmeshpro/docs/rich-text/)列出了常用标记语言。

# Overflow

当文字溢出容器边界时，组件提供了几种可控的方式。

| Overflow模式 | 说明                     |
| ------------ | ------------------------ |
| Overflow     | 直接向下连续溢出垂直边界 |
| Elipsis      |                          |
| Masking      |                          |
| Truncate     |                          |
| ScrollRect   |                          |
| Page         |                          |
| Linked       | 溢出到另一个Text容器中   |

# 实现图文混排

可以通过右键切好的图集创建一个SpriteAsset，并使用富文本标记来将图片混排在文本中。步骤如下：

* 创建图集对应的SpriteAsset；

![](D:\gzq\blog\ANKIIMA.github.io\source\_posts\TextMeshPro\3.png)

* 设置引用关系，有三种方式，可以全局设置Default Sprite Asset，或者组件中自行绑定，还可以在TMP Settings中根据设置的Sprite Assets路径通过富文本标记访问。

![](D:\gzq\blog\ANKIIMA.github.io\source\_posts\TextMeshPro\4.png)

![](D:\gzq\blog\ANKIIMA.github.io\source\_posts\TextMeshPro\5.png)

如果组件的SpriteAsset为空，那么将默认使用Default Sprite Asset，在文本中添加标记：

```
<sprite=0>
```

可以对图集中inde为0的图片进行引用。如果想单独指定使用的图集，在文本中添加标记：

```
<sprite="图集对应SpriteAsset名称" index=0>
<sprite="图集对应SpriteAsset名称" name="图片在图集中名称">
```

组件会自动将这类标记转换为图片。图集的各项属性和其中图片的设置可以在Sprite Asset的Inspector中进行编辑，主要是设置偏移量和缩放以符合需要。
