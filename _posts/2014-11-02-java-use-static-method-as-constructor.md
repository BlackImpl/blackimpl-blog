---
layout:     post
title:      使用静态工厂方法代替构造器
date:       2014-11-02 15:42:57
summary:    Effective 读书笔记 <一> 使用静态工厂方法代替构造器
categories: java
---


###类实例获取
  类实例的获取，最常用的方法是提供一个公有的构造器。这种方法通常是大家在写程序的时候最常用的方法, 当我们想要一个类的实例的时候, 只需要new 这个类的公有构造器即可。

###静态工厂方法替代构造器
  这里的静态构造方法不同于设计模式中的工厂方法模式。这里的静态工厂方法, 指的是返回类实例的静态方法。类可以通过静态工厂方法提供实例, 而不需要通过new 公有构造器。这样做有几大优势:

   * ####静态工厂方法有名字。
     构造方法是没没名字的。当有多个构造方法的时候, 这一堆带着不同参数的构造方法, 人们是永远不会记得那个构造方法是干嘛用的的。静态工厂方法就是本质就是一个普通的静态方法,函数名可根据实际意义来取。

   * ####静态工厂方法不用每次调用它们的时候都创建一个新的实例。

     如果程序要经常创建相同的对象, 而且对象创建的开销还很高, 那么使用静态工厂方法来替代构造器将会极大的提升你程序的性能。使用静态工厂方法, 可使不可变类可以使用预先构造好的实例, 或将构造好的实例缓存起来, 进行重复利用, 从而避免创建重复的对象。

   * ####静态工厂方法可以返回原返回类型的任何子对象。

     Java collection Framework 中的多种集合类型,几乎所有的这些实现都在一个不可实例化的类(java.util.Collection)导出。

   * ####在创建参数化类型实例的时候,代码更简洁。

     例如, 使用HashMap 默认构造器, 你需要写下着堆代码:
      {% highlight java %}
        Map<String, List<String>> map = new HashMap<String, List<String>>();
      {% endhighlight %}

     使用静态工厂方法来实例化:

      {% highlight java %}
         public static <k, v> HashMap<k, v> newInstance(){//获取HashMap 的静态实例化工厂方法
              return new HashMap<k,v>();
         }
      {% endhighlight %}

     实例化HashMap的代码则简化为:

     {% highlight java %}
         Map<String, List<String>> map = HashMap.newInstance();
     {% endhighlight %}

  当然, 凡事都具有两面性, 既然使用静态构造方法能带来这么多好处, 那就必定会产生相应的坏处。

   * 类如果不含protected或者public 的构造器的话,就不能被子类化。

     不过不能被子类化的话,就无法使用继承。不过这样的话, 自然鼓励使用组合。所以说，塞翁失马,焉知非福。

   * 静态工厂方法类本质就是静态方法。

     Javadoc 工具只会把这个方法当做一个静态方法看待,不仔细看文档,可能不知道怎么实例化。
     
