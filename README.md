[合集 \- Ideal库 \- Common库(2\)](https://github.com)[1\.开源 \- Ideal库 \- 常用时间转换扩展方法（一）11\-07](https://github.com/hugogoos/p/18531206):[悠兔机场](https://xinnongbo.com)2\.开源 \- Ideal库 \- 常用时间转换扩展方法（二）11\-09收起
书接上回，我们继续来分享一些关于时间转换的常用扩展方法。


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241108171350524-378340297.png)


# ***01***、时间转日期时间 TimeOnly


该方式是把TimeOnly类型转为DateTime类型，其中日期部分使用系统当前日期，时间部分则使用TimeOnly，具体代码如下：



```
//时间转日期时间，默认使用当前日期+时间转为日期时间格式
public static DateTime ToDateTime(this TimeOnly timeOnly)
{
    return DateOnly.FromDateTime(DateTime.Now).ToDateTime(timeOnly);
}

```

# ***02***、日期\+时间转为日期时间 TimeOnly


该方法是直接对TimeOnly时间进行扩展，取用其时间，然后补全指定DateOnly日期，最后转为DateTime，代码如下：



```
//日期+时间转为日期时间
public static DateTime ToDateTime(this TimeOnly timeOnly, DateOnly dateOnly)
{
    return dateOnly.ToDateTime(timeOnly);
}

```

# ***03***、日期时间中日期部分\+时间转日期时间 TimeOnly


该方法是对TimeOnly时间进行扩展，取其时间，然后补全指定DateTime中的日期部分，最后再转为DateTime，代码如下：



```
//日期时间中日期部分+时间转日期时间格式
public static DateTime ToDateTime(this TimeOnly timeOnly, DateTime dateTime)
{
    return DateOnly.FromDateTime(dateTime).ToDateTime(timeOnly);
}

```

# ***04***、日期转日期时间 DateOnly


该方式是把DateOnly类型转为DateTime类型，其中日期部分使用DateOnly，时间部分则使用系统当前时间，具体代码如下：



```
//日期转日期时间，日期+默认使用当前时间转为日期时间格式
public static DateTime ToDateTime(this DateOnly dateOnly)
{
    return dateOnly.ToDateTime(TimeOnly.FromDateTime(DateTime.Now));
}

```

# ***05***、日期\+日期时间中时间部分转日期时间 DateOnly


该方法是对DateOnly日期进行扩展，取其日期，然后补全指定DateTime中的时间部分，最后再转为DateTime，代码如下：



```
//日期+日期时间中时间部分转日期时间
public static DateTime ToDateTime(this DateOnly dateOnly, DateTime dateTime)
{
    return dateOnly.ToDateTime(TimeOnly.FromDateTime(dateTime));
}

```

# ***06***、日期时间中日期部分\+时间转日期时间 DateTime


该方法是对DateTime日期时间进行扩展，取其日期部分，然后补全指定TimeOnly时间，最后再转为DateTime，代码如下：



```
//日期时间中日期部分+时间转日期时间
public static DateTime ToDateTime(this DateTime dateTime, TimeOnly timeOnly)
{
    return DateOnly.FromDateTime(dateTime).ToDateTime(timeOnly);
}

```

# ***07***、日期\+日期时间中时间部分转为日期时间 DateTime


该方法是对DateTime日期时间进行扩展，取其时间部分，然后补全指定DateOnly日期，最后再转为DateTime，代码如下：



```
//日期+日期时间中时间部分转为日期时间
public static DateTime ToDateTime(this DateTime dateTime, DateOnly dateOnly)
{
    return dateOnly.ToDateTime(TimeOnly.FromDateTime(dateTime));
}

```

# ***08***、日期时间转日期 DateTime


该方法是对DateTime日期时间进行扩展，取其日期部分转为DateOnly，代码如下：



```
//日期时间转日期，保留日期时间中日期部分
public static DateOnly ToDateOnly(this DateTime dateTime)
{
    return DateOnly.FromDateTime(dateTime);
}

```

# ***09***、日期时间转时间 DateTime


该方法是对DateTime日期时间进行扩展，取其时间部分转为TimeOnly，代码如下：



```
//日期时间转时间，保留日期时间中时间部分
public static TimeOnly ToTimeOnly(this DateTime dateTime)
{
    return TimeOnly.FromDateTime(dateTime);
}

```

上面的这些扩展方法虽然大多都是简单调用原生方法，但是通过扩展方法方式提供出来后的确在编码上提升了一些效率，代码也会根据简洁。


# ***10***、代码结构设计与组织


代码结构的设计和组织是软件开发过程中重要的一环，它将直接影响代码的可读性、可维护性、扩展性、测试性和团队协作的效率。


到目前为止我们关于时间的扩展方法已经有接近30个方法了，后面还有很多，目前都是放在DateTimeExtension类下面，如下图：


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241108171407657-1745953904.jpg)


到这里已经能感受到代码结构显得很混乱了，直观感觉就是可读性差，没有结构化必然会导致扩展困难。


可能会有人提出不同观点，这些不就是很多静态方法吗，只有排版整齐就行了，其实不然，我们可以借助一些小技巧把方法集合结构化的管理起来。


结构化的管理一种实现方式就行——分类。下面我们就对现有代码进行分类。


分类是一门技术活，如何选择分类标准很关键，比如我们这里可以根据入参类型分为针对long类型转换、针对DateTime类型转换、针对string类型转换；也可以根据功能分类比如时间和时间戳互相转换是一类，时间和字符串互相转换是一类，DateTime与DateOnly和TimeOnly互相转换是一类等等分类方式。


## 1、分大类


我的思路是这样的，首先通过部分类partial把DateTimeExtension根据入参类型分成几个大类。


代码整理后效果如下：


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241108171415931-1797818770.png)


## 2、分小类


分完大类后发现long类型中方法还是很大，因此我们继续对其进行分类，我们根据功能不同分为两类：转为本地日期时间和转为UTC日期时间。我们可以使用代码折叠的预处理指令\#region和\#endregion来处理处理小分类。


代码整理以后效果如下：


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241108171424101-1310476893.png)


从一开始一堆方法到现在结构层次已经非常清晰了，阅读起来也非常方便，后期维护扩展就可以很容易的快速找到相应地方处理。


# ***11***、单元测试


作为一个开源代码，我们首要任务应该保证代码正确性，因此单元测试是必不可少的。单元测试不但可以保证我们代码的正确性，还可以促使我们代码写的跟健壮。


单元测试我们以同样的分类方式进行代码组织，代码大致如下：


![](https://img2024.cnblogs.com/blog/386841/202411/386841-20241108171435076-712652516.png)


因为具体的测试用例都比较简单，这里就不拿出来讲解了。


测试用例编写的越完善，我们代码就越可靠，因此我们在编写单元测试时候，要尽可能覆盖所有逻辑，要尽可能覆盖到一些特例情况，我们想的越多代码质量才能越高。


# ***12***、文档


作为一个开源代码，还需要一个好的文档，这样别人才能轻松使用，目前文档还是缺失的，后续我将陆续添加上。


当代码结构设计与组织、单元测试、文档这三部分都做好了，我感觉这个开源代码就达到开源给别人的最低要求了。后面的开源代码我都将围绕这三部分展开，希望可以给大家分享一些高质量的开源代码。


稍晚些时候我会把库上传至Nuget上，大家可以搜索Ideal.Core.Common直接使用。


***注***：测试方法代码以及示例源码都已经上传至代码库，有兴趣的可以看看。[https://gitee.com/hugogoos/Ideal](https://github.com)


