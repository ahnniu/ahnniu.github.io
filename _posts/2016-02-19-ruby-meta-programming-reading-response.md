---
layout: post
title: 《Ruby元编程》读后感
---

最近读了下 《Ruby元编程》, 收获颇多, 以下是关于一些概念的理解.

下面是示例代码,和对象模型. 下文都是基于这个例子展开的.


```ruby
def top_level_private_method
  'Object#top_level_private_method'
end

def top_level_public_method
  'Object#top_level_public_method'
end

def self.top_level_singleton_method
  'Object#top_level_singleton_method'
end

class C
  def initialize(value = 0)
    @a_value = value
  end

  def self.c_class_method
    'C.c_class_method'
  end

  def c_public_method
    "C#c_public_method with a_value = #{@a_value}"
    
  end

private
  def c_private_method
    "C#c_private_method with a_value = #{@a_value}"
  end

protected
  def c_protected_method
    "C#c_protected_method with a_value = #{@a_value}"
  end
end

class D < C
  attr_accessor :x, :y
  protected :x=, :y=

  def initialize(x = 0, y = 0)
    @x, @y = x, y
  end

  def swap(other)
    tmp_x, tmp_y = @x, @y
    @x, @y = other.x, other.y
    other.x, other.y = tmp_x, tmp_y
    return self
  end
end

obj = D.new

class << obj
  def a_singleton_method
    'obj#a_singleton_method'
  end
end
end
```


![对应的对象模型图](/images/post/ruby-meta-programming-thinking-figure.draw.png)

__对象模型图, 使用draw.io画的: [源文件](/images/post/ruby-meta-programming-thinking-figure.draw.io)__

## 关于对象模型

在Ruby中,处处是对象. 变量仅仅是一个对象的引用(类似C语言的指针). 对象通过C.new类方法实例化. 一个对象是一个内存区域,区域里包含:

- 各个实例变量(像上面说的,变量其实就是一个指针地址)
- 一个所属类的引用


## 关于类,模块

类也是一个对象, 不同于普通的对象, 一个类包含了类实例方法定义, 类的superclass是模块, 类与模块的区别仅仅是:

- 类可以实例化
- 类可以继承

一般来说, 模块中定义的方法仅可以通过mixin到一个类中使用.但是也可以定义像类方法类似的模块方法.

```ruby
module M
  def mixin_method
    'can only use with a class, M.mixin_method not work'
  end

  def self.moudle_method
    'can be called by M.moudle_method, not work in a mixin class'
  end
end
```


## 关于方法调用

### 方法调用时序

方法调用要搞清楚几点:

- 从对象模型中找到对应的方法,若找不到,则会调用 默认的method_missing(), 如若需要,可以重写method_missing()魔法实现Ghost method.
- 每一行代码执行都需要一个当前对象(self), 需要搞清楚self到底是谁
    + 当方法调用时, 接收者就是self
    + 所有实例变量都是self对象的实例变量
- 每一个方法必须指定一个接收者, 如果没有指定,将使用隐含接收者self

### private / protected / public 方法

private方法:

- 如若指定接收者(即便写的是self.), 不能调用该方法
- 子类可以调用父类的private方法, 如果对应的方法中有实例变量, 这些变量也将是子类实例化对象的实例变量

protected方法:

- 首先可以像private那样用
- 仅在同一个类(派生类)中时可将该方法作为实例方法调用, 主要用于同一个类(派生类)不同对象之间的数据交换(当数据没有公开接口时)

示例: 参考代码的class D

### initialize()方法

initialize()是一个private方法, 仅在Class.new()的时候调用.

#### 如何使用不同的构造函数

- 方法一: 缺省参数
- 方法二: 定义不同的类方法,在类方法中调用new()


## 关于作用域


- 仅当遇到module, class, def关键字, 作用域才会切换.
- ruby中的作用域无嵌套一说,即不同的作用域就是不同的作用域,无内部作用域可以看到外部作用域的说法
- 进入class C定义,当前作用域self = C
- 进入module M定义, 当前作用域self = M
- 方法,仅当方法调用时,才进入方法的作用域, self=接收者对象
- 进入块, 不会切换作用域

## 关于顶层类 和顶层对象

- 顶层对象是main, 是一个Object对象.
- 顶层类是Object, 在顶层定义的方法会添加到Object的private_instance_methods


