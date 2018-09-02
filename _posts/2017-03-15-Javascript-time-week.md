---
layout: post
title:  "从JS原生Date类型浅谈web开发兼容性"
categories: Javascript
tags:  Javascript web 小程序
author: Romennts 
---


最近在魔改一个校园信息系统微信小程序，在计算当前教学周，需要获取特定日期的周数。大家知道，在java世界里面调用   **Calendar.WEEK_OF_YEAR** 就可以得到指定日期的周数了，可是小程序前端开发只能使用Javascript，而在js中没有类似这样的方法直接获取。

最容易的方法就是每次都向服务器获取教学周，可这太重量级，为了一个变量发送一次请求，每次打开小程序都要请求一次数据，再渲染，非常的耗时。于是上网找到一个function关于js 计算指定日期的周数，搬运工就这样以为成了，可发现原作者计算方法有误，传入的date对象如果是当天的上午为 N 周，如果传入的值是同一天的下午则为 N+1 周，这导致课表上午显示正常，下午显示就不正常了，以为是我计算错了，没有把开始的那周计算进去，细细排查发现是网上搬过来的function在取模的时候有误。




于是传入的对象不再是 new Date() 就完事，而是变成new Date(‘'2017-03-01'’) 这样，得到的data对象按道理应是这样：Wed Mar 01 2017 08:00:00 GMT+0800 (China Standard Time) ，的确在安卓端是这样的，小程序运行正常，教学周显示正常，然而用iphone测试，教学周一直显示 null ，期间我把所有的传入的值都使用了parseInt()，问题照旧，获取不到周数，就无法正常显示当天课程，到这时候不管你们崩溃没，反正我是已经想死了…….这时候发现问题大了，赶紧把小程序下线，要不别人以为当天没课。于是借了土豪的手机来开发，临近崩溃边缘，吐槽微信开发工具，模拟出来的iphone和真正的iphone完全是两码事，很多特征不一样，css布局都差很多，还有这个教学周问题，在模拟器上玩得好好的。接着调试代码一直跟踪到new data()对象本身，竟然发现在iphone new Date(‘'2017-03-01'’)会得到 Invalid Date ，我的天，什么回事，谜一样的，谷歌” Javascipt Invalid Date”查到 new Date()在解析一个具体的时间的时候，对参数有较严格的格式要求，格式不正确的时候会直接返回 **Invalid Date**  。

那么new Date()能接受的参数格式到底是什么标准呢?显然到了这里可以知道，ios和android 执行的标准不一样，new Date()传入字符串格式需要满足 **RFC2822** 标准或者 **ISO 8601** 标准，我用的是ISO 8601标准日期字符串 **（YYYY-MM-DDThh:mm:ss）** ，而RFC2822 标准日期字符串长这样的：**YYYY/MM/DD HH:MM:SS ± timezon(时区用4位数字表示)** ，按道理有ISO应该好很多，毕竟是国际标准化组织的东西，然而事实并非如此， **RFC2822** 兼容性才是最好的，比如 **IE8、Safari、iOS** 均不支持ISO 8601。IE这种东西你可能会不屑，不过iOS也有这个坑，不得不去提防，所以建议大家还是用 **RFC2822** 。回到小程序，在new Date()传入参数的标准改为RFC2822，兼容性问题解决，终于计算出准确的教学周。之后为了更优雅的完成教学周的计算，通过改写Date实例，定义schoolweek获取器，它会返回当前到指定时间的周数间隔。简单地访问该属性就会调用事先定义好的函数，无需显示调用。来源不明的函数还是要多加留意，如果是引入不明依赖，水更深，特别是在开发重要的系统，更需要严格审核每个依赖包。

```js
/**
 * 更优雅的完成教学周的计算
 * {Date} BEGIN_DAY    开学时间
 * @return {Number}    当前教学周
 */
    Date.prototype.__defineGetter__('schoolweek', function(){
      var BEGIN_DAY = new Date('2017/02/27');
      var diff = ((this.getTime() - BEGIN_DAY.getTime()) / 1000), 
      day_diff = Math.floor(diff / 86400);
      console.log(day_diff);
      return day_diff < 0 && "0" ||
             day_diff < 7 && "1" ||
             Math.ceil( (day_diff + 1) / 7 );      
    });

    var a = new Date('2017/03/28');
    console.log(a.schoolweek); //5
```
 
说到这里兼容性问题已经非常凸显，应了那句话，一进前端深似海~~一直以为只有IE是浏览器一大奇葩，现在多了一朵，傲娇的iOS系统居然也不支持**ISO 8601**日期字符串，其实在web开发过程中，兼容性多是浏览器执行标准的问题。IE逐渐退出舞台，微软都放弃了，我想应该无需什么保留了，但面对iOS就不能那么任性了，顺便说一下，一开头说的 **Calendar.WEEK_OF_YEAR**  是Java 的 **SimpleDateFormat** jar，建议不要使用，推荐使用Java8 Time，SimpleDateFormat也是挺坑的，简单点就是 SimpleDateFormat 时间对象序列化成字符串之后，反序列化成和原对象表达不是同一个时间的时间对象了，如果是维护遗留项目没法改的话小心点咯，新项目建议尽量不要使用SimpleDateFormat jar。
 
在前端两个月玩得挺开心的，作为Java的忠粉，准备做完这个轮子后弃小程序坑了~~用javascript这种弱类型语言太久，回到Java的世界，感觉能创造一个宇宙。
 
希望对看到这篇文章的人有点帮助。

#### 本文首发于个人公众号： 后端之巅