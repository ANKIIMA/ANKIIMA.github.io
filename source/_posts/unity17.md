---
title: unity进阶(一)-数据存储和持久化
date: 2023-08-21 21:51:47
categories:
- Computer Graphics
- Engine
- Unity
tags:
- CG
- Game
- Unity
---

经过两天研究，是时候总结下unity的数据存储方式了。

之前我们的工程里全部用类或者静态类保存数据，维护起来很不方便，尤其对于策划来说没有直接的工作流，所以我们得提供一个配表解决方案，让策划能够通过修改Excel或者其它文本格式的文件来完成属性的配置。而且有时候需要用网络传输数据文件，我们程序里的数据是不能直接传输的，这就涉及到序列化和反序列化。下面我看看Unity中一般有哪些存储数据的方式。

<!--more-->

# 数据持久化

之前写代码总是让数据在声明的时候进行初始化，那么这个类每次被实例化的时候就会初始化一次该对象，这样做有两个缺点：第一，如果类是单例还好，可有时需要让这个类作为预制件的脚本挂上去，后续还会实例化这个预制件，那么我们这样会浪费很多空间用来声明这些值，尽管它们有可能是相同的。

第二，每次我们启动游戏，所有的状态都会被重置，因为类在实例化的时候重新声明了数据值，没办法保存上一次打开游戏的数据状态，这就是数据的持久化不能实现。

数据持久化指的是，让数据能够保存在硬盘等外部硬件资源，供后续使用的存储方式。在类的内部进行数据的声明，不能实现数据的持久化。因此我们要以文件作为媒介将数据保存下来。

# 序列化和反序列化

了解了持久化，我们再介绍序列化是什么。由于程序运行中某一时刻的状态无法直接保存，我们需要将运行在程序中的数据转化为文件，这样才能用网络通信的接口进行传输或自己保存在本地，这就是序列化。Unity编辑器中提供了Inspector窗口查看程序的值，它的实现就使用了序列化，将运行过程中变量的值序列化为某种格式，然后展示到窗口中。

我们自己实现序列化说白了就是将某个时候的一个或多个变量值存在某种格式的文件中，例如JSON，XML等，这样通信将这些文件传输出去，或者下次打开的时候读取这些文件，就能知道这个时候的状态是什么了。

反序列化就是上面说的读取的这个过程，收到了一个JSON文件，此时对它进行解析，将其中的值重新赋值给我们的变量，就完成了反序列化。通过序列化和反序列化，可以实现网络通信的部分要求和数据持久化。

# Scriptable Object

这个神器本身是很好用的，但是由于它只支持编辑器的数据持久化，打包以后就不能了，所以有的教程用这个做背包简直是毫无用处，只能在编辑器里看看效果，真要做背包还是得数据库。但是这个东西优点也很多，比如可以在编辑器里查看、修改，而且资源加载的时候也不用自己再手动反序列化一次，省了一点时间。

[这位](https://www.zhihu.com/question/40879788)大佬贴了一个用SO做数据存储的解决方案，可以瞻仰一下。

Unity中新建一个类，继承ScriptableObject即可创建一个Scriptable Object类，你可以通过添加序列化关键字的方式来配置它管理的内容，并且支持嵌套，用一个Root类管理其它Scriptable Object，其余完全和类变量的序列化一致。

唯一区别是你不用将它挂载到对象上实例化，而是通过Editor添加一个创建Scriptable Object的选项，在Project中创建该资产，后缀为asset，这样就可以直接使用了，在Inspector中修改序列化的值，然后就能

# JSON序列化和反序列化

## 反序列化

当我们的应用接收到一个JSON文件，需要进行解析，对应到我们自己创建的实体类中，其实就是反序列化。首先需要建立对应JSON文件的实体类，推荐一个[网站](https://www.json.cn/json/json2csharp.html)可以自动生成JSON文件对应的实体类。当然自己写也可以，只是有可能会出错。

```json
[
  {
    "NAME": "Vardan",
    "HP": 100.0,
    "ATK": 30.0,
    "DEF": 20.0
  }
]
```

通过这两个方法就可以实现序列化和反序列化，但是要注意处理的都是字符串，要解析文件的内容还需要进一步将JSON文件转化为字符串才行。而字符串也因为JSON的格式有不同的限制，这里以二维表格为例，在JSON中一个对象是用{}括起来的，对象可以组成数组，用[]括起来，JSON反序列化的API解析的是单个对象，因此需要注意自己处理的JSON文件的格式是什么，特别是要将数组转化为对象的List，单独对每个对象进行反序列化，不然就会出错。

我这里生成的JSON文件都是默认数组，因此尽管这里文件中只有一个对象，还是用[]括起来了。它生成的数据类是：

```C#
public class AIConfig
{
    /// <summary>
    /// 
    /// </summary>
    public string NAME { get; set; }
/// <summary>
/// 
/// </summary>
public int HP { get; set; }
/// <summary>
/// 
/// </summary>
public int ATK { get; set; }
/// <summary>
/// 
/// </summary>
public int DEF { get; set; }
}

public class Root
{
    /// <summary>
    /// 
    /// </summary>
    public AIConfig AIConfig { get; set; }
}
```

注意，后面Root类也可以不要，声明为public以后不使用属性也能够获得类，自动生成的数据类为了方便JSON的嵌套会额外加一个Root，实际上需要的就是匹配的数据类就行了。

使用newtonsoft来解析，Unity自带的方法不足够解析嵌套的JSON文件，所以我们采用这个第三方插件完成，这个是最推荐的，还有另外两个方法：JsonLit和JsonUtility，后者是Unity自带的，一般情况也足够使用。

- JsonConvert.DeserializeObject(json);将一个json对象的字符串解析成一个类对象
- JsonConvert.SerializeObject;将一个类对象转化成一个json字符串对象

通过上面的API可以实现序列化和反序列化了，我们先读取json文件：

```C#
		//json路径
        string filePath = "Assets/Resources/Data/Config_AI.json";
        //如果没找到就返回
        if(File.Exists(filePath) == false) 
        {
            Debug.Log("File not Found");
            return;
        }
        //读取文件为字符串
        string json = File.ReadAllText(filePath);
```

读取以后解析，如果是一个{}包括的JSON对象，直接使用：

```C#
		AIConfig root = JsonConvert.DeserializeObject<AIConfig>(json);
```

但是我们这里是JSON数组，因此必须要使用：

```C#
		List<AIConfig> roots = JsonConvert.DeserializeObject<List<AIConfig>>(json);
```

这样才能单独处理每个对象，否则会报错。之后json文件的配置信息就已经读取到数据类AIConfig中了，你可以通过拷贝方法来完成赋值或者初始化，这就涉及到深拷贝和浅拷贝的知识了，后面用到再说，现在直接等号赋值就完了。
