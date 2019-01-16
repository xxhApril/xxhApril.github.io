---
layout: post
title: iOS面试宝典 开发工具-Swift
date: 2019-01-16 11:05:46
tags: ios 面试 swift
---

#iOS面试宝典 - 第三章  语言工具

####[1. Swift]()

####[2. Objective-C]()

####[3. Swift VS Objective-C]()

####[4. Xcode 使用]()


## 1. Swift

在iOS开发中，语言选择是最初的一步，现在苹果公司主推的语言是 Swift。

Swift 自 2014年发布以来，已经历经 4 个版本的迭代。其在 TIOBE 编程语言排行榜上，一度位列第 12 位，超过 Ruby 并远远甩开上一代语言 Objective-C。从性能上来说，它的速度是 Objective-C 的 2.6 倍，是 Python 的 8.4 倍。更重要的是，Swift 是一门开源的语言，它的性能和优化接受者整个业界的监督和关注。无论从哪个角度讲，Swift 都将取代 Objective-C，成为 iOS 开发的主流语言。

所以，在面试中，关于 Swift 的问题越来越多。对于这门语言的熟悉程度，成为判断一名软件工程师水准的重要标准之一。下面的内容会从实战和理论连个角度来探讨 Swift 的特性。


### Swift 面试理论题

#### 类（class）和结构体（struct）有什么区别

**关键词**：<font color="red">```#引用类型```  ```#值类型```</font>
 
在 Swift 中，class 是引用类型，struct 是值类型。值类型在传递和赋值时将进行复制，而引用类型只会适用引用对象的一直“指向”。所以，两者之间的区别就是两个类型的区别。

举一个简单的例子，代码如下：

```
class Temperature {
	var value: Float = 30.0
}

class Person {
	var temp: Temperature?
	
	func sick() {
		temp?.value = 40
	}
}

let A = Person()
let B = Person()
let temp = Temperature()

A.temp = temp
B.temp = temp

A.sick()
```

在上面这段代码，Temperature 是 class，为引用类型，故 A 的 temp 和 B 的 temp 指向同一个对象。A 的 temp 值被修改了，B 的 temp 值也会随着改变。如果将 Temperature 改为 struct，为值类型，则 A 的 temp 值的修改不会影响到 B 的 temp 值。

在内存中，引用类型，诸如类，是在堆（heap）上进行存储和操作的；而值类型，诸如结构体，是在栈（stack）上进行存储和操作的，相比栈上的操作，堆上的操作更加复杂和耗时，所以苹果官方推荐使用结构体，这样可以提高 APP 的运行效率。

**加分回答：**

class 的如下功能是 struct 没有的：

- 可以继承，这样子类可以使用父类的特性和方法。
- 类型转换可以再运行时检查和解释一个实例的类型。
- 可以用 deinit 来释放资源。
- 一个类可以被多次引用。

struct 也有如下优势：

- 结构较小，适用于复制操作，相比一个 class 的实例被多次引用，struct 更加安全。
- 无须担心内存泄漏或者多线程冲突问题。

**类似问题：**

引用类型和值类型有什么区别？struct 相比 class，在使用上有什么优势？

#### Swift 是面向对象还是函数式的编程语言

**关键词**：<font color="red">```#面向对象```  ```#函数式编程```</font>

Swift 既是面向对象的编程语言，又是函数式的编程语言。

说 Swift 是面向对象的编程语言，是因为 Swift 支持类的封装、继承和多态，从这一点来看，Swift 与 Java 这类纯面向对象的编程语言几乎毫无差别。

说 Swift 是函数是的编程语言，是因为 Swift 支持 map，reduce，filter，flatmap 这类除去中间状态、数学函数式的方法，更加强调运算结果而不是中间过程。

**类似问题：**

为什么说 Swift 是函数式的编程语言？

#### 在 Swift 中，什么是可选型（optional）

**关键词**：<font color="red">```#Optional```  ```#nil```</font>

在 Swift 中，可选型是为了表达当一个变量值为空的情况。当一个变量值为空时，它就是 nil。在 Swift 中，无论变量是引用类型还是值类型，都可以是可选型变量。下面举一个例子：

```
/// 值类型为 Float，value 默认值是 30.0
var value: Float = 30.0
/// 值类型为 String ,key 默认值为 nil
var key: String?
/// 引用类型为 UIImage， image 默认值为 nil
var image: UIImage?
```

**加分回答：**

在 Objective-C 中没有明确提出可选型的概念，然而，其引用类型却可以为 nil，以此来标志其变量值为空的情况。而 Swift 将这一概念扩大到值类型，并且明确提出了可选性的概念。

#### 在 Swift 中，什么是泛型（Generics）
**关键词**：<font color="red">```#泛型```</font>

在 Swift 中，泛型主要是为了增加代码的灵活性而生的：它可以使对应的代码满足任意类型的变量或方法。

举一个简单的例子：加入要写出一种方法可以交换两个 Int 值，其中一种写法如下：

```
func swap(_ a: inout Int, _ b: inout Int) {
    (a, b) = (b, a)
}
```

上面这种写法有用但并不高效。因为，加入需要实现一个方法用于交换两个 Float 值，那么又得重写一遍。泛型就是为了解决这类问题而来的：希望有个一般性的方法，可以交换任意类型的值。

```
func swap<T>(_ a: inout T, _ b: inout T) {
    (a, b) = (b, a)
}
```

**注意**，Swift 是类型安全的语言，所以这里交换两个变量的类型必须一致。

#### 说明比较关键词：Open，Public，Internal，File-private和private

**关键词**：<font color="red">```#访问控制权限```</font>

Swift 有五个级别的访问控制权限，从高到底一次为空 Open，Public，Internal，File-private 和 private。

它们遵循的基本原则是：高级别的变量不允许被定义为低级别变量的成员变量。比如，一个 Private 的 class 中不能含有 Public 的 String。反之。低级别的变量却可以定义在高级别的变量中。比如，Public 的 class 中可以含有 Private 的 Int 值。

- **Open** 具备最高的访问权限。其修饰的的类和方法可以在任意 Module 中被访问和重写；它是 Swift3 中新添加的访问权限。
- **Public** 的权限仅次于 Open。与 Open 唯一的区别在于，它修饰的对象可以在任意 Module 中被访问，但不能重写。
- **Internal** 是默认的权限。它表示只能在当前定义的 Module 中被访问和重写。它可以被一个 Module 中的多个文件访问，但不能被其他 Module 访问。
- **File-private** 也是 Swift3 中新添加的权限。其修饰的对象只能在当前文件中使用。例如，它可以被一个文件中的 class、extension 和 struct 共同使用。
- **Private** 是最低级别的访问权限。它的对象只能在定义的作用域内使用。离开了这个作用域，即使是同一个文件中的其他作用域，也无法访问。

**类似问题：**

Swift3 中新引入的 Open 和 File-private 关键词有什么用？

#### 说明比较关键词：Strong，Weak 和 Unowned

**关键词**：<font color="red">```#引用类型```    ```#内存管理``` </font>

Swift 的内存管理机制与 Objective-C 一样，都为 ARC（Automatic Reference Counting）。其基本原理是，一个对象在没有任何强引用指向它时，所占用的内存就会被回收。反之，只要有任何一个强引用指向该对象，它就会一直存在于内存中。

- Strong 代表强引用，是默认属性。当一个对象被声明为 Strong 时，表示父层级对该对象有一个强引用的指向，此时，该对象的引用计数会增加1。
- Weak 代表弱引用。当一个对象被声明为 Weak 时，表示父层级对该对象没有指向，该对象的引用计数不会增加1。在该对象被释放后，弱引用页随即消失。继续访问该对象，程序会到的 nil，不会崩溃。
- Unowned 与弱引用的本质一样。唯一不同的是，该对象被释放后，依然有个无效的引用指向对象，它不是 Optional，也不指向 nil。如果继续访问该对象，则程序就会崩溃。

**加分回答：**

引入 Weak 和 Unowned，是为了解决由 Strong 带来的循环引用问题。简单地说，当两个对象互相有一个强引用指向对方时，就会导致两个对象在内存中无法被释放。

Weak 和 Unowned de 使用场景有如下差别：

- 当访问对象可能已经被释放时，则用 weak，比如 delegate 的修饰。
- 当访问对象确定不可能被释放时，则用 unowned，比如 self 的引用。

实际上，为了安全，很多公司规定任何时候都用 Weak 修饰。

#### 在 Swift 中，如何理解 copy-on-write

**关键词**：<font color="red">```#内存管理``` </font>

当值类型（比如 struct）在复制时，复制的对象和原对象实际上在内存中指向同一个对象。而且仅当修复制后的对象时，才会在内存中重新创建一个新的对象。下面举一个例子：

```
/// arrayA 是一个数组，为值类型
let arrayA = [1, 2, 3]
/// arrayB 这个时候与 arrayA 在内存中是同一个数组，内存中没有生成新的数组
var arrayB = arrayA
/// arrayB 被修改了，此时 arrayB 在内存中变成了一个新的数组，而不是原来的 arrayA 了
arrayB.append(4)
```

从上面的代码可以看出，复制的数组与原数组共享一个地址，直到其中一个发生改变。这样的设计使得值类型可以被多次复制而无须耗费多余的内存，只有变化的时候才会增加开销。**因此内存的使用更加高效**。

#### 什么是属性观察器（Property Observer）

**关键词**：<font color="red">```#willSet```    ```#didSet``` </font>

```
var title: String {
    willSet {
        print("将标题从\(title)设置到\(newValue)")
    }
    didSet {
        print("将标题从\(oldValue)设置到\(title)")
    }
}
```

上面这段代码对 title 进行了监听。在 title 发生改变前，willSet 对应的作用域将被执行，新的值是 newValue。在 title 发生改变后，didSet 对应的作用域将被执行，原来的值为 oldValue。这就是属性观察。

**加分回答：**

初始化方法对属性的设定，以及在 willSet 和 didSet 中对属性的再次设定，都不会触发调用属性观察器。


### Swift 面试实战题

#### 在结构体中如何修改成员变量的方法

**关键词**：<font color="red">```#mutating``` </font>

请问下面的代码存在什么问题？

```
protocol Pet {
    var name: String { get set }
}

struct MyDog: Pet {
    var name: String
    
    func changeName(name: String) {
        self.name = name
    }
}
```

应该在 ```func changeName(name: String)``` 的前面加上关键词 ```mutating```,表示该方法会修改结构体中自己的成员变量。

**注意**，再设计协议的时候，由于 protocol 可以被 class 或 struct 或 enum 实现，故而要考虑是否用 `mutating` 来修饰方法。

类中不存在这个问题，因为类可以随意修改自己的成员变量。


#### 如何用 Swift 实现 或（||）操作

**关键词**：<font color="red">```#autoclosure``` </font>

这道题的解法很多，下面给出一种最直接的解法：

```
func ||(left: Bool, right: Bool) -> Bool {
    if left {
        return true
    } else {
        return right
    }
}
```

上面这种解法虽然正确，当时并不高效。或（||）操作的本质是，当表达式左边的值为真的时候，无需计算表达式右边的值。而上面这种解法是将表达式右边的值准备好，在传入进行操作。当表达式右边的值十分复杂时，就会造成性能上的浪费。所以，上面的这种做法违反了或（||）操作的本质，正确的实现方法如下：

```
func ||(left: Bool, right: @autoclosure () -> Bool) -> Bool {
    if left {
        return true
    } else {
        return right()
    }
}
```

autoclosure 可以将表达式右边值得计算推迟到判定 left 值为 false 时，这样就可以避免第一种方法带来的不必要的计算开销了。

#### 实现一个函数：输入任意一个整数，输出为输入的整数 +2

**关键词**：<font color="red">```#柯里化``` </font>

这道题看似简单，如果直接写成下面的代码：

```
func addTwo(_ num: Int) -> Int {
    return num + 2
}
```

接下来面试官会问：“假如要实现输入为输出的整数+6或+8呢？能不能只定义一次方法呢？”正确的写法是利用 Swift 的 Currying 特性：

```
func add(_ num: Int) -> (Int) -> Int {
    return { val in
        return num + val
    }
}
let addTwo = add(2), addFour = add(4), addTen = add(10)
```

Swift 的柯里化特性是函数式编程思想的体现。它接受多个参数的方法进行变形，并用高阶函数的方式进行处理，是整个代码更加灵活。

#### 实现一个函数：求 0~100（包括 0 和 100）中为偶数并且恰好是其他数字方法的数字

**关键词**：<font color="red">```#函数式编程``` </font>

这套题十分简单。最简单、最粗暴的写法如下：

```
func evenSquereNums(from: Int, to: Int) -> [Int] {
    var res = [Int]()
    for num in from ... to where num % 2 == 0 {
        if (from ... to).contains(num * num) {
            res.append(num * num)
        }
    }
    return res
}
evenSquereNums(from: 0, to: 100)
```

上面代码的写法虽然正确，但是不够优雅。首先，这个方法完全可以利用泛型进行优化，同时，可以在创建 res 数组时加上 reserveCapacity，以保证其性能。其实这个题目最简单、直接的写法使用函数式编程的思想，一行代码即可解决问题：

```
(0 ... 10).map { $0 * $0 }.filter { $0 % 2 == 0 }
```

Swift 有函数式编程的思想。其中 flatMap，map，reduce 和 filter 是其代表的方法。本题考察了 map 和 filter 的组合使用。相比一般的 for 循环，这种写法更加简洁。

