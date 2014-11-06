---
layout:     post
title:      使用Builder模式替代构造器
date:       2014-11-06 23:01:23
summary:    Effective 读书笔记 <二> 使用Builder模式替代构造器
categories: java
---

在写代码的时候, 我们发现经常很多类会在构造实例的时候,会有多个构造参数,用来构造不同状态的实例。

###重载构造方法,构造不同状态的实例
静态工厂方法和普通的构造器有个共同的局限：当一个类有多个构造参数时,不能很好的扩展到大量的可选参数。当一个类需要使用不同的参数来进行初始化的时候,通常的做法是重载构造器。例如以下代码:

{% highlight java %}
public class Student{

  private int id;
  private String name;
  private int age;

  public Student(int id, String name, int age){
    this.id = id;
    this.name = name;
    this.age = age;
  }

  public Student(int id, String name){
    this(id, name, 18);
  }

  public Student(int id, int age){
    this(id, null, age);
  }

}
{% endhighlight %}

当我们想要一个不同初始状态的Student的实例的时候，选择其中的一个构造器就好了:

{% highlight java %}
  Student student = new Student(1,15);
{% endhighlight %}

用这种方法,三个构造参数的时候,看起来这代码还行,毕竟最多也就7个构造函数。但当你的类有N个构造参数的时候, 那别人拿到你的类都不知道怎么实例化了。所以多个构造参数的时候,重载构造方法可行,但是过于繁杂。

###使用Java Bean 获取实例
Java Bean看起来是一个不错的解决方法。你字需要为你的构造参数添加上get/set方法即可(还能由IDE自动添加),何乐而不为。例如以下代码：

{% highlight java %}
public class Student{

  private int id;
  private String name;
  private int age;

  public void setId(int id){
    this.id = id;
  }

  public int getId(){
    return this.id;
  }

  public void setName(String name){
    this.name = name;
  }

  public int getName(){
    return this.name;
  }

  public void setage(int age){
    this.age = age;
  }

  public int getAge(){
    return this.age;
  }

}
{% endhighlight %}

Java Bean 这种模式明显的弥补了重载构造器的不足,这样代码十分简单,而且便于阅读:

{% highlight java %}
Student student = new Student();
student.setId(1);
student.setAge(15);
student.setName("test");
{% endhighlight %}

遗憾的是, Java Bean 本身有着非常严重的缺点: 你的实例在调用过程中可能处于不一致的状态。 Java Bean不能创建不可变的实例,因为需要你付出额外的代价来保证这个类是线程安全的。

###使用Builder模式替代构造器

Builder 模式轻松解决了多个构造参数时，重载构造方法带来的复杂性和不便阅读性,以及Java Bean 模式带来的状态不一致问题。Builder模式在保证可读性的同时,也保证了安全性。不需要你做额外的同步,对象的实例一旦创建,状态就不会再变化。下面是示例:

{% highlight java %}
public class Student{

  private final int id;
  private final String name;
  private final int age;

  private Student(Builder builder){
    this.id = builder.id;
    this.name = builder.name;
    this.age = builder.age;
  }

  public static class Builder(){
    //必选构造参数
    private final int id;

    //可选构造参数,默认值
    private String name = "";
    private int age = 15;

    public Builder(int id){
      this.id = id;
    }

    public Builder name(String val){
      name = val;
      return this;
    }

    public Builder age(int val){
      age = val;
      return this;
    }

    public Student build(){
      return new Student(this);
    }

  }

}
{% endhighlight %}

这时候,要获取一个Student对象的示例代码如下:

{% highlight java %}
Student student = Student.Builder(1).age(25);
{% endhighlight %}

###总结
Builder模式使用方法来设置可变参数,不仅代码便于阅读,而且你有多少个构造参数都无所谓,而且创建出来的实例状态不会再改变。当然,比起重载构造器,没次你要创建实例,都必须先创建Builder的构造器,这在注重性能的环境下,会是一笔开销,而且在代码更冗长。总的来说,如果类的构造器有多个构造参数,Builder模式还是一个不错的选择。
