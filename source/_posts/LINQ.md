---
title: C#的语言集成查询LINQ
date: 2024-05-24 11:11:37
tags:
- C#
categories:
- C#
---

LINQ是一种很方便的查询工具，是一系列直接将查询功能集成到C#语言的技术统称。开发中常用的查询如果使用LINQ编写，能够大幅度节省代码，但性能上会略有影响，本编博客记录LINQ在开发场景下的主要应用。

<!--more-->

## 扩展方法

了解LINQ语句之前首先解释如何实现，C#通过扩展方法在不新建派生类型的情况下为现有类型添加方法。LINQ是最常见的扩展方法，将查询功能添加到[System.Collections.IEnumerable](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.ienumerable)和[System.Collections.Generic.IEnumerable&lt;T&gt;](https://learn.microsoft.com/zh-cn/dotnet/api/system.collections.generic.ienumerable-1)中，这些查询方法通常使用Lamda表达式作为参数。

扩展方法形式上通过非嵌套、非泛型的静态类内静态方法实现，例如下面是对string类型定义的扩展方法:

```C#
namespace ExtensionMethods
{
    public static class MyExtensions
    {
        public static int WordCount(this string str)
        {
            return str.Split(new char[] { ' ', '.', '?' },
                             StringSplitOptions.RemoveEmptyEntries).Length;
        }
    }
}
```

第一个参数需要用this修饰，后面是需要扩展的类型和名称，虽然是静态方法，但是可以通过实例进行调用，编译器的中间语言会将代码转换为对该静态方法的调用。

## LINQ的查询操作

LINQ的查询操作可以通过三个步骤完成：

* 获取数据源
* 创建查询
* 执行查询



其中，数据源是支持IEnumrable\<T\>接口的数据类型如数组、列表等，foreach语句同样需要类型支持该接口才能遍历，被称为可查询类型。使用C#查询关键字可以方便地进行查询，语法类似数据库查询操作：

| 子句                                                         | 说明                                                         |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| [from](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/from-clause) | 指定数据源和范围变量（类似于迭代变量）。                     |
| [where](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/where-clause) | 基于由逻辑 AND 和 OR 运算符分隔的一个或多个布尔表达式筛选源元素。 |
| [select](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/select-clause) | 指定执行查询时，所返回序列中元素的类型和形状。               |
| [group](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/group-clause) | 根据指定的密钥值对查询结果分组。                             |
| [into](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/into) | 提供可作为对 join、group 或 select 子句结果引用的标识符。    |
| [orderby](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/orderby-clause) | 根据元素类型的默认比较器对查询结果进行升序或降序排序。       |
| [join](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/join-clause) | 基于两个指定匹配条件间的相等比较而联接两个数据源。           |
| [let](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/let-clause) | 引入范围变量，在查询表达式中存储子表达式结果。               |
| [in](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/in) | [join](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/join-clause) 子句中的上下文关键字。 |
| [on](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/on) | [join](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/join-clause) 子句中的上下文关键字。 |
| [equals](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/equals) | [join](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/join-clause) 子句中的上下文关键字。 |
| [by](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/by) | [group](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/group-clause) 子句中的上下文关键字。 |
| [ascending](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/ascending) | [orderby](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/orderby-clause) 子句中的上下文关键字。 |
| [descending](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/descending) | [orderby](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/keywords/orderby-clause) 子句中的上下文关键字。 |

最主要的是from、where、select三个子句，from指定数据源，where指定筛选器，select指定返回的元素类型，举例如下：

```C#
// Program.cs
// The Main() method

static IEnumerable<string> Suits()
{
    yield return "clubs";
    yield return "diamonds";
    yield return "hearts";
    yield return "spades";
}

static IEnumerable<string> Ranks()
{
    yield return "two";
    yield return "three";
    yield return "four";
    yield return "five";
    yield return "six";
    yield return "seven";
    yield return "eight";
    yield return "nine";
    yield return "ten";
    yield return "jack";
    yield return "queen";
    yield return "king";
    yield return "ace";
}

// Program.cs
public static void Main(string[] args)
{
    var startingDeck = from s in Suits()
                       from r in Ranks()
                       select new { Suit = s, Rank = r };

    foreach (var c in startingDeck)
    {
        Console.WriteLine(c);
    }
}
```

上述代码将suits中的s和ranks中的r一一配对，生成含有52个元素的IEnumerable类型。查询本身不会做任何数据操作，而是延迟执行在foreach对查询变量枚举时，因此每次对延迟执行的数据源查询都可能会得到不同结果，如果需要强制执行可以使用ToList或者ToArray方法。因此和普通的语法查询相比，使用LINQ需要注意对查询变量及时保存，除非确有延迟查询的需要。

## 自定义查询扩展方法

LINQ的查询不满足对自定义类型的查询要求，需要重新定义扩展方法。继续上面代码的例子，现在我们对这副扑克牌进行洗牌操作，也就是52张牌分成上下两半，然后交替重叠到一起。

```C#
public static IEnumerable<T> InterleaveSequenceWith<T>
    (this IEnumerable<T> first, IEnumerable<T> second)
{
    var firstIter = first.GetEnumerator();
    var secondIter = second.GetEnumerator();

    while (firstIter.MoveNext() && secondIter.MoveNext())
    {
        yield return firstIter.Current;
        yield return secondIter.Current;
    }
}
```

由于是迭代器方法，所以可以使用yield return来返回单个元素。最后在Main方法中调用这个洗牌方法：

```C#
// Program.cs
public static void Main(string[] args)
{
    var startingDeck = from s in Suits()
                       from r in Ranks()
                       select new { Suit = s, Rank = r };

    foreach (var c in startingDeck)
    {
        Console.WriteLine(c);
    }

    var top = startingDeck.Take(26);
    var bottom = startingDeck.Skip(26);
    var shuffle = top.InterleaveSequenceWith(bottom);

    foreach (var c in shuffle)
    {
        Console.WriteLine(c);
    }
}
```

如果多次执行洗牌操作，会发现LINQ性能上的问题，我们看下面的代码：

```C#
// Program.cs
static void Main(string[] args)
{
    // Query for building the deck

    // Shuffling using InterleaveSequenceWith<T>();

    var times = 0;
    // We can re-use the shuffle variable from earlier, or you can make a new one
    shuffle = startingDeck;
    do
    {
        shuffle = shuffle.Take(26).InterleaveSequenceWith(shuffle.Skip(26));

        foreach (var card in shuffle)
        {
            Console.WriteLine(card);
        }
        Console.WriteLine();
        times++;

    } while (!startingDeck.SequenceEquals(shuffle));

    Console.WriteLine(times);
}

```

由于延迟查询，每次洗牌过程中首先Take方法和Skip方法各对纸牌进行了查询，然后foreach处又需要查询一次，那么一次洗牌实际上花费三次LINQ查询。上面的代码统计几次洗牌后牌序会和原本一致，那么每执行一次洗牌，都会因为LINQ语句在shuffle被引用的时候进行多余查询，导致性能严重下降。

## 使用举例

下面代码使用LINQ的扩展方法ToDictionary将字典的value全部设置为false，字典的key是一个自定义的枚举类型。

```C#
qualityChooseStateDic = qualityChooseStateDic.ToDictionary(pair => pair.Key, pair => true);
```

该扩展方法将IEnumrable类型转换为字典，接受两个lamda表达式作为参数，实际上类型是Func&lt;Tsource, Tkey&gt;和Func&lt;Tsource, Tvalue&gt;。使用这类方法可以节省代码，不然对字典的赋值需要编写循环方法。
