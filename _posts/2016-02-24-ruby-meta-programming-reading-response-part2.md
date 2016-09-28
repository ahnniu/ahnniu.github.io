---
layout: post
title: 《Ruby元编程》读后感Part2 - 作用域
---

Ruby中, 变量是对象的引用,也可以叫做绑定. 作用域是代码的执行环境:变量, 实例变量, self. 作用域切换, 变量,self的绑定也会跟着变化. 没有嵌套作用域的说法, 进入一个新的作用域, 那么必然从上一个作用域退出.

## 什么时候执行代码:

- 在Class/Module定义中的一些执行语句.
- 顶级类/上下文中的一些执行语句.
- 方法只有调用时候才会执行.

## 什么时候切换作用域:

- 当遇到class/module关键字定义类时
    + 遇到class/module关键字,进入类的作用域,然后遇到对应的end,从当前类作用域返回到上一层类作用域,直到顶层类作用域(Object)
    + 当前类作用域中: self绑定的是类本身,比如定义MyClass类, self表示MyClass这个类
- 当遇到def关键字定义方法时
    + 方法的接收者(调用对象)会充当方法体内的self
    + 如果方法没有显示的接收者,则会把隐性的self继续充当方法体内的self
    + 如若是类的实例方法, 实例变量在同一个类中的不同方法中可见, 类变量不可见, 类方面需要使用MyClass.class_method()调用
    + 局部变量仅仅可见方法体内定义的局部变量, 类定义中的局部变量不可见

## 块

### 块的存在形式

块始终作为方法的参数存在.

- Proc对象, 把块转变为对象保存起来,后续通过call()调用
    + Proc.new { }
    + lambda { }
    + proc { }
- 作为函数调用的参数,这种最常见,跟在一个方法调用的后面.
    + names.each { }

### 块绑定

如上面所说, 块始终跟在一个方法的调用作为参数存在. 块不会切换作用域, 块定义时的作用域是一样的. 

- 如下面示例代码, 块中self的绑定是顶级对象main, 而不是方法的接收者obj
- 块中的局部变量, 会依次绑定:
    + 首先看是不是块的参数, 若是块的参数则与其绑定
    + 然后看块定义时的作用域是否有定义同名变量, 若有,则绑定, 做读写
    + 上述两个都找不到, 然后定义块局部变量, 仅仅可用于块中, 块退出, 则取消绑定.(如果未先初始化,则使用, 变量的值会是nil)

```ruby
class MyClass
  def my_method
    yield
  end
end

obj = MyClass.new
obj.my_method { self }  # => main
```

#### 块绑定的例外 instance_eval()


```ruby
class MyClass
  def my_method
    yield
  end
end

obj = MyClass.new
obj.instance_eval { self }  # => #<MyClass:0x00000000b1c420> 
```

`instance_eval()` 与普通的块绑定有一个地方不同, self的绑定:

- 普通的块self, 看定义块时作用域中的self
- `instance_eval`的self则传入接收者对象
    + 正因为self绑定了对象obj, 所以可以在块中时候对象的实例方法(包含privte方法),实例变量
- 局部变量绑定同普通块


`class_eval()`与 `instance_eval()`类似, 不同的是操作的是一个类(类其实也是一个Class对象)


### 闭包

同一时间,只会存在一个作用域, 进入一个作用域, 必然从另外一个作用域退出. 而切换作用域门是方法定义, 类定义, 模块定义. 常规方法下, 很难在不同的方法, 方法与类 之间共享变量.
当需要跨方法, 类定义访问同一个变量时, 需要用到闭包, 核心要点是用块来代替class, def这些关键字, 从而使得作用域没有切换.

- class关键字 可用 Class.new { } 代替
- def关键字 可用 define_method :method_name { }代替

### 块的对象化

- 可以通过 lambda, Proc.new proc把一个块变成一个对象存储起来, 后续可通过 Proc#call()方法执行块
- &操作符, 可以在Proc对象与块之间转换
