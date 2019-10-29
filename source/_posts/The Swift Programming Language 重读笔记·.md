## The Swift Programming Language 重读笔记

### 前言

`SwiftGG` 翻译组的 [《The Swift Programming Language》in Chinese](https://swiftgg.gitbook.io/swift/) 我在 `Swift3` 的时候通读过一遍，在 `Swift4` 的时候只浏览了前半部分，并未通读。现在 `Swift5` 准备再重新读一遍文档，把一些遗忘的、易忘的、感觉值得记下的知识点梳理一遍，以方便记忆。


#### 基础部分

1. 一般来说，很少需要写类型注释(type animation)，如果在声明常量或者变量时赋了一个初始值， `Swift` 可以推断出这个常量或变量的类型。

2. 如果需要使用 `Swift` 保留关键字相同的名称作为常量或变量名，可以使用反引号将关键字包围的方式将其作为名字使用。无论如何，我们都应当避免使用关键字作为常量或变量名，除非别无选择。

3. 当遇到一些相关值得简单分组时，元组是很有用的。元组不适合用来创建复杂的数据结构。如果数据结构比较复杂，不要使用元组，用类或者结构体去建模。

4. `if` 与 `guard` 用户区别：在 `if` 条件语句中使用常量或变量来创建一个可选绑定，仅在 `if` 语句的句中 `body` 中才能获取到值。在 `guard` 语句中使用常量或变量创建一个可选绑定时，仅在 `guard` 语句外且在语句后才能获取到值。

5. 断言和先决条件的不同点是，他们什么时候进行状态检测：断言仅在调试环境运行，而先决条件则在调试环境和生产环境中运行。在生产环境中，断言的条件将不进行评估。这意味着我们可以再开发阶段使用很多断言，但是这些断言再生产环境都不会产生影响。


#### 基本运算符

1. 在对负数 `b` 求余时，`b` 的符号会被忽略。这意味着 `a % -b` 和 `a % b` 是一样的结果。但是 `-a % b` 与 `a % b` 结果并不一样。

2. 一元正负号 `+` 不做任何改变的返回操作数的值。 虽然一元操作符什么都不会改变，但当使用一元负号 `-` 来表达负数时，可以使用一元正好来表达正数，使代吗具有对称美。

   ```Swift
    let minusSix = -6
    let alsoMinusSix = +minusSix  // alsoMinusSix 等于 -6
    ```
    
3. 如果两个元组的元素相同，且长度相同的话，元组就可以被比较，比较元组大小会按照从左到右、逐值比较的方式，直到发现两个值不相等时停止，如果所有值都相等，那么我们就称这一对元组是相等的。`Swift` 标准库只能比较七个以内元素的元组。如果元组元素超过七个，则需要自己实现比较运算符。

    ```Swift
    (1, "zebra") < (2, "apple")   // true，因为 1 小于 2
    (3, "apple") < (3, "bird")    // true，因为 3 等于 3，但是apple 小于 bird
    (4, "dog") == (4, "dog")      // true，因为 4 等于 4，dog 等于 dog
    ```


4. 空合运算符 `??`。ps. 其实我一直我不清除这个叫啥名字。。


#### 字符串和字符

1. 多行字符串字面量，是由一对三个双引号包裹着的具有固定顺序的文本字符集。也可以在行尾写一个反斜杠 \ 作为续行符。
    
   ```Swift
     let softWrappedQuotation = """
        The White Rabbit put on his spectacles.  "Where shall I begin, \
        please your Majesty?" he asked.

        "Begin at the beginning," the King said gravely, "and go on \
        till you come to the end; then stop."
        """
    ```

    
2. 关于 `字符串字面量的特殊字符`，主要是转义字符、`Unicode` 标量。平时使用较少。还有转义字符反斜杠 \ 的使用。


3.  `扩展字符串分隔符` `#`。将字符串文字放在扩展分隔符中，这样字符串中的特殊字符将会被直接包含而不是转移后的效果。如果想要转义字符效果，用等量 `#` 包裹住即可。

4. 字符串、结构体、枚举，都是值类型。

5. 字符串知识中，关于 `Unicode` ，比较零碎，实际开发中也很少用到，已读。

6. 关于计算字符数量，需要注意的是通过 `count` 属性返回的字符数量并不总是与包含相同自负的 `NSString` 的 `length` 属性相同。 `NSString` 的 `length` 属性是利用 `UTF-16` 表示的十六位代码单元数字，而不是 `Unicode` 可扩展的字符群集。

7. 不同的字符可能会占用不同数量的存储空间，要以要知道字符串中 `Character` 的确定位置，就必须从 `String` 开头遍历每一个 `Unicode` 标量到结尾。因此， `Swift` 的字符串不能整数 `integer` 做索引。

8. 子字符串 `SubString` 和 `String` 区别在于性能优化上。`SubString` 可以重用原 `String` 的内存空间，或者另一个 `SubString` 的内存空间（`String` 也有同样的优化，但如果两个 `String` 共享内存的话，它们就会相等）。这一优化意味着你在修改 `String` 和 `SubString` 之前都不需要消耗性能去复制内存。就像前面说的那样，`SubString` 不适合长期存储 —— 因为它重用了原 `String` 的内存空间，原 `String` 的内存空间必须保留直到它的 `SubString` 不再被使用为止。


#### 集合类型

1. 关于数组，每天都在用，要强调的不多。这里说三个方法及区别：`removeLast()` ，移除数组最后一个元素并返回该元素，数组必须不为空，否则会引发运行时崩溃； `popLast()` ，移除数组最后一个可选类型的元素，也就是说，数组可以为空； `dropLast()` ，移除掉最后几个元素，并返回移除后的原数组。

    ```Swift
    var digitArr = [1,2,3,4,5]
    var last1 = digitArr.removeLast()
    var last2 = digitArr.popLast()
    var remain = digitArr.dropLast(2)
    print(last1, last2 ?? "-2", remain)
    // 输出： 5 4 [1]
    ```

2. 集合 `Set`。用来存储相同类型并且没有顺序的值。当集合元素顺序不重要并且确保每个元素只出现一次时，可以使用集合而不是数组。可自己定义类型作为集合的值类型，但是定义的类型要遵循 `Hasshable` 协议，而 `Hashable` 协议遵循了 `Equatable` 协议，所以定义的类型符合这些协议即可。

3. 集合提供了一些方法可以高效完成对集合的操作。

    - 使用 `intersection(_:)` 方法根据两个集合的交集创建一个新的集合。
    - 使用 `symmetricDifference(_:)` 方法根据两个集合不相交的值创建一个新的集合。
    - 使用 `union(_:)` 方法根据两个集合的所有值创建一个新的集合。
    - 使用 `subtracting(_:)` 方法根据不在另一个集合中的值创建一个新的集合。

4. 集合还提供了方法来判断集合之间的关系：

    - 使用“是否相等”运算符 `==` 来判断两个集合包含的值是否全部相同。
    - 使用 `isSubset(of:)` 方法来判断一个集合中的所有值是否也被包含在另外一个集合中。
    - 使用 `isSuperset(of:)` 方法来判断一个集合是否包含另一个集合中所有的值。
    - 使用 `isStrictSubset(of:)` 或者 `isStrictSuperset(of:)` 方法来判断一个集合是否是另外一个集合的子集合或者父集合并且两个集合并不相等。
    - 使用 `isDisjoint(with:)` 方法来判断两个集合是否不含有相同的值（是否没有交集）。


4. 字典 `Dictionary` 是一个无序集合，存储键值之间的关系。为了以特定的顺序遍历字典的键或值，可以对字典的 `keys` 或 `values` 使用 `sorted()` 方法。如果想要移除字典中的相关元素，可以可以使用如下两种方式：

    ```Swift
    var ages = ["Tom": 20, "jack": 12, "Lilei": 18]
    ages.removeValue(forKey: "Tom")
    print(ages)
    /// ["Lilei": 18, "jack": 12]
    ages["KK"] = nil
    print(ages)
    /// ["Lilei": 18, "jack": 12]
    ages["jack"] = nil
    print(ages)
    /// ["Lilei": 18]
    ```


#### 控制流

1. `Swift` 提供了多种流程控制结构，包括可以多次执行任务的 `While` 循环，基于特定条件选择执行不同代码分支的 `if` 、 `guard` 、 `Switch` 语句，还有控制流程跳转到其他位置的 `break` 和 `continue` 语句。

    `Swift` 还提供了 `for-in` 循环，用来更简单的遍历数组、字典、区间、字符串和其他序列类型。
    
2. `for-in` 循环中，值得注意的有一点： `stride(from:to:by:)` 用于半开半闭区间， `stride(from:through:by:)` 用于闭区间，都是间隔循环。示例如下：
    
    ```Swift
    let minutes = 15
    let minuteInterval = 5
    for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
        print("tickMark = ", tickMark)
    }
    /// 输出： 0  5  10
    for tickMark in stride(from: 0, through: minutes, by: minuteInterval) {
        print("tickMark2 = ", tickMark)
    }
    /// 输出： 0  5  10  15
    ```

3. `while` 循环从计算一个条件开始，如果条件为 `true` ，会重复运行一段代码，直到条件变为 `false`。`repeat-while` 和 `while` 的区别在于判断循环条件之前，先执行一次循环的代码块。然后重复循环直到条件为 `false`。

4. `Switch` 在 `Swift` 中是非常强大、灵活的。支持区间匹配、元组匹配、值绑定、`where` 条件、复合型 `case` 以及包含相同值绑定的复合型 `case` 等。

5. `Swift` 共有五种控制转移语句：
    `continue`，`break``fallthrough`，`return`，`throw`。
    
    - `continue` ：告诉一个循环立即停止本次循环，重新开始下一次的循环。但是不会离开整个循环。
        
    - `break`：立刻结束整个控制流的执行。 `break` 在循环中，是结束该循环。在 `Switch` 中，是结束该 `Switch`。

    - `fallthrough`：在 `switch` 中使用，该关键字不会检查它下一个将会落入执行的 `case` 中的匹配条件。`fallthrough` 简单地使代码继续连接到下一个 `case` 中的代码。这和 `C` 语言标准中的 `switch` 特性是一样的。


6. 带标签的语句。这个我个人确实没有用到过。这里记一下。简单来说，就是给语句命名，然后控制转移语句直接针对特性语句做处理，下面是代码：

    ```Swift
    let finalSquare = 25
    var board = [Int](repeating: 0, count: finalSquare + 1)
    board[03] = +08; board[06] = +11; board[09] = +09; board[10] = +02
    board[14] = -10; board[19] = -11; board[22] = -02; board[24] = -08
    var square = 0
    var diceRoll = 0

    gameLoop: while square != finalSquare {
        diceRoll += 1
        if diceRoll == 7 { diceRoll = 1 }
        switch square + diceRoll {
        case finalSquare:
            // 骰子数刚好使玩家移动到最终的方格里，游戏结束。
            break gameLoop
        case let newSquare where newSquare > finalSquare:
            // 骰子数将会使玩家的移动超出最后的方格，那么这种移动是不合法的，玩家需要重新掷骰子
            continue gameLoop
        default:
            // 合法移动，做正常的处理
            square += diceRoll
            square += board[square]
        }
    }
    print("Game over!")
    ```

7. 提前退出语句 `guard` 。

8. 检测 `API` 可用性，只要是针对不同系统版本的兼容。通用写法：

    ```Swift
    if #available(平台名称 版本号, ..., *) {
        APIs 可用，语句将执行
    } else {
        APIs 不可用，语句将不执行
    }
    ```
    实际案例展示：
    
    ```Swift    
    if #available(iOS 10, macOS 10.12, *) {
        // 在 iOS 使用 iOS 10 的 API, 在 macOS 使用 macOS 10.12 的 API
    } else {
        // 使用先前版本的 iOS 和 macOS 的 API
    }
    ```


#### 函数

1. 函数的可变参数。一个可变参数 `variadic parameter` 可以接受零个或多个值。通过在变量类型名后面加入 `...` 的方式来定义可变参数。传入值在函数体中变为此类型的一个数组。一个函数中最多只能有一个可变参数。

    ```Swift
    func arithmeticMean(_ numbers: Double...) -> Double {
        var total: Double = 0
        for number in numbers {
            total += number
        }
        return total / Double(numbers.count)
    }
    arithmeticMean(1, 2, 3, 4, 5)
    // 返回 3.0, 是这 5 个数的平均数。
    arithmeticMean(3, 8.25, 18.75)
    // 返回 10.0, 是这 3 个数的平均数。
    ```

2. 定义输入输出函数，在参数定义前加 `inout` 关键字即可。注意，只能传变量给输入输出参数，不能传常量或字面量，因为这些量是不能被修改的。输入输出参数不能有默认值，而且可变参数不能使用 `inout` 标记。


#### 闭包

1. 关于闭包表达式的进化。虽然越变越简单，但是，还是应该以清晰明了为主。示例只是对闭包语法的一个说明：

    ```Swift
    /// 排序数组
    let names = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
    /// 1.0 版本
    func backward(_ s1: String, _ s2: String) -> Bool {
        return s1 > s2
    }
    var reversedNames = names.sorted(by: backward)
    print(reversedNames)
    // reversedNames 为 ["Ewa", "Daniella", "Chris", "Barry", "Alex"]

    /// 2.0 版本
    reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in
        return s1 > s2
    })

    /// 3.0 版本
    reversedNames = names.sorted(by: { (s1: String, s2: String) -> Bool in return s1 > s2 } )

    /// 4.0 版本
    reversedNames = names.sorted(by: { s1, s2 in return s1 > s2 } )

    /// 5.0 版本
    reversedNames = names.sorted(by: { s1, s2 in s1 > s2 } )

    /// 6.0 版本
    reversedNames = names.sorted(by: { $0 > $1 } )

    /// 最终版
    reversedNames = names.sorted(by: >)
    ```

2. 闭包是引用类型！！

3. 在逃逸闭包内必须显示的调用 `self` ，非逃逸闭包则必须要。所谓逃逸闭包，就是指函数返回后才会执行的闭包。典型的就是网络闭包，在请求结束后才会执行。还有自动闭包 `autoclosure` ，但是这个并不建议使用过多。

    ```Swift
    override func viewDidLoad() {
        super.viewDidLoad()
        
        someFunctionWithNonescapingClosure {
            x = 50
        }
        someFunctionWithEscapingClosure {
            self.x = 100
        }
        someFuncationWithAutoclosureClosure(closure: "Hello")
    }
    
    /// 非逃逸闭包
    func someFunctionWithNonescapingClosure(closure: () -> Void) {
         closure()
    }
    /// 逃逸闭包
    func someFunctionWithEscapingClosure(closure: @escaping () -> Void) {
        closure()
    }
    /// 自动转化闭包
    func someFuncationWithAutoclosureClosure(closure: @autoclosure () -> String) {
        print("\(closure())")
    }
    ```

#### 枚举

1. 令枚举遵循 `CaseIterable` 协议。`Swift` 会生成一个 `allCases` 属性，用于一个包含枚举所有成员的集合。如：

    ```Swift
    enum Beverage: CaseIterable {
        case coffee, tea, juice
    }
    let numberOfChoices = Beverage.allCases.count
    print("\(numberOfChoices) beverages available")
    // 打印“3 beverages available”
    ```
    
2. 递归枚举是一种枚举类型，它由一个或多个枚举成员使用该枚举类型的实例作为关联值。用递归枚举时，编译器会插入一个间接层。可以在枚举成员前加上 `indirect` 来标识该成员可递归。
    
    例如，下面的例子，枚举存储了简单的算术表达式：
    ```Swift
    enum ArithmeticExpression {
        case number(Int)
        indirect case addition(ArithmeticExpression, ArithmeticExpression)
        indirect case multiplication(ArithmeticExpression, ArithmeticExpression)
    }
    ```
    
    也可以再枚举类型开头加上 `indirect` 关键字来表明它的所有成员都是可递归的：
    
    ```Swift
    indirect enum ArithmeticExpression {
        case number(Int)
        case addition(ArithmeticExpression, ArithmeticExpression)
        case multiplication(ArithmeticExpression, ArithmeticExpression)
    }
    ```
    
    
####     类和结构体

1. `Swift` 中机构体和类有很多共同点，两者都可以：

    - 定义属性有存储值
    - 定义方法用于提供功能
    - 定义下标操作用于通过下标访问它们的值
    - 定义构造器用于生成初始值
    - 通过扩展增加默认实现以外的功能
    - 遵循协议提供某些标准功能
    
    与结构体相比，类还有如下的附加功能：
    
    - 继承，允许一个类继承另一个类的特征
    - 类型转换，允许在运行时检查和解释一个类实例的类型
    - 析构器，允许一个类实例释放任务其所被分配的资源
    - 引用计数，允许对一个类多次引用
    
    类增加的附加功能是以增加复杂性为代价的，作为一般准则，优先使用结构体，因为它们更容易理解，仅在适当或必要时使用类。实际上，这意味着我们大多数自定义的数据类型都应该是结构体和枚举。
    
2.  恒等运算符
 
    因为类是引用类型，所以多个常量或变量幕后可能引用同一个类实例。对于结构体和枚举来说，这并不成立，因为他们都是值类型，再被赋予到常量、变量或者传递到函数时，其值总是会被拷贝。

    判定两个常量或者变量是否引用同一个类实例又是会非常有用。为了达到这个目的，`Swift` 提供了两个恒等运算符，使用这两个运算符检测两个常量或者变量是否引用同一个类实例：
    
    - 相同 `===`
    - 不相同 `!==`
    
    请注意，**相同** (用三个等号标识 `===`) 与 **相等** (用两个等号表示 `==`) 的不同，**相同** 表示两个类类型(`class type`) 的常量或变量是否引用同一个类实例，**相等** 用于表示两个值是否相等或等价，判定时要遵循设计者定义的评判标准。
    
####     属性

1.  全局的常量或者变量都是延迟计算的，跟延时加载存储属性相似，不同的地方在于，全局的常量或者变量不需要标记 `lazy` 修饰符。而局部范围的常量或变量从不延迟计算。

2.  **类型属性**，不论创建多少一个类的类实例，类型属性都只有唯一一份，所有实例共享该数据。使用 `static` （用于类和结构体），或者 `class`（用于类）关键字作为修饰符。

####  方法

1.  方法是与某些特性类型相关联的函数。譬如实例方法、类方法等。


2.  在实例方法中修改值类型

    结构体和枚举都是值类型，默认情况下，值类型的属性不能在它的实例方法中被修改。
    
    但是，如果确定需要在某个特定的方法中修改结构体或者枚举的属性，可以为这个方法选择 **可变** `mutating` 行为，然后就可以在方法内部改变它的属性。
    
    在可变方法中可以给 `self` 重新赋值：
    
    ```
    enum TriStateSwitch {
        case off, low, high
        mutating func next() {
            switch self {
            case .off:
                self = .low
            case .low:
                self = .high
            case .high:
                self = .off
            }
        }
    }
    var ovenLight = TriStateSwitch.low
    ovenLight.next()
    // ovenLight 现在等于 .high
    ovenLight.next()
    // ovenLight 现在等于 .off
    ```
    
    
    
####     下标

1.  下标允许通过在实例名称后面的方括号中传入一个或者多个索引值来对实例进行查询。它的语法类似于实例方法语法和计算型属性语法。定义下标使用 `subscipt` 关键字，与定义实例方法类似，都是指定一个或多个输入参数和一个返回类型。与实例方法不同的是，下标可以设定为读写或只读。这种行为由 `getter` 和 `setter` 实现，类似计算型属性：

    ```
    subscript(index: Int) -> Int {
        get {
             // 返回一个适当的 Int 类型的值
         }
        set(newValue) {
            // 执行适当的赋值操作
        }
    }
    ```
    
    
2. 实例下标是在特定类型的一个实例上调用的下标。也可以定义一种在这个类型自身上调用的下标。这种下标被称作类型下标。可以通过在 `subscript` 关键字之前写下 `static` 关键字的方式来表示一个类型下标。类类型可以使用 `class` 关键字来代替 `static`，它允许子类重写父类中对那个下标的实现。下面的例子展示了如何定义和调用一个类型下标：

    ```
    enum Planet: Int {
        case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
        static subscript(n: Int) -> Planet {
            return Planet(rawValue: n)!
        }
    }
    let mars = Planet[4]
    print(mars)
    ```
3. 补充一个 `static` 与 `class` 的区别：
    
    - `static` 可以修饰属性、函数，可用于类或者结构体中。但是被 `static` 修饰的对象不支持重写 `override`。
    - `class` 只用于类对象中，可以重写。
    
    

####     继承

1. 在合适的地方，可以通过使用 `super` 前缀来访问超类版本的方法、属性或者下标：
    - 在方法 `someMetho()` 的重写实现中，可以通过 `super.someMethod` 来调用超类版本的 `someMethod()` 方法。
    - 在属性 `someProperty` 的 `getter` 或 `setter` 的重写实现中，可以通过 `super.someProperty` 来访问超类版本的 `someProperty` 属性。
    - 在下标的重写实现中，可以通过 `super[someIndex]` 来访问超类版本中相同的下标。 
    

2. 可以把方法、属性、下标标记为 `final` 来防止它们被重写。只需要在声明关键字前加上 `final` 修饰符即可。也可以在关键字 `class` 前添加 `final` 修饰符，来将整个类标记为 `final` 。这样的类是不可以被继承的。


#### 构造过程

1. 类和结构体在创建实例时，必须为所有存储属性设置合适的初始值。存储属性不能处于一个未知的状态。当为存储属性设置默认值或者在构造器中设置初始值时，他们的值是被直接设置的，不会触发任何属性观察者。


2.  关于 **形参命名** 和 **实参标签**

    下面代码块中， `fromFahrenheit` 是实参标签，`fahrenheit` 是形参命名。

    ```
    struct Celsius {
        var temperatureInCelsius: Double
        init(fromFahrenheit fahrenheit: Double) {
            temperatureInCelsius = (fahrenheit - 32.0) / 1.8
        }
    }
    ```
    
3.  可以再构造过程中的任意点给常量属性赋值，只要在构造过程结束时它设置成正确的值。一旦长两属性被赋值，它将永远不能更改。对于类实例来说，它的常量属性只能在类的构造过程中被修改，不能在子类中修改。

4.  结构体如果没有定义任何自定义构造器，它将自动获得一个 **逐一成员构造器**（memberwise initializer）。不像默认构造器，空手存储型属性有默认值，结构体也会获得逐一成员构造器。

5. 构造器可以调用其他构造器来完成实力部分的构造过程。这一过程成为构造器代理。

6.  `Swift` 为类提供了两种构造器来确保实例中所有的存储型属性都能获得初始值，它们被称为指定构造器和便利构造器。指定构造器是类中最主要的构造器。其中：
    - 指定构造器必须总是 **向上代理**
    - 便利构造器必须总是 **横向代理**
    
7.  安全检查的两段式构造过程展示：

    **阶段 1**
    - 类的某个指定构造器或便利构造器被调用。
    - 完成类的新实例的内存分配，但此时内存还没有被初始化。
    - 指定构造器确保其所在的类引入的所有存储型属性都已赋初值。存储型属性的内存完成初始化。
    - 指定构造器切换到父类的构造器，对其存储属性完成同样的任务。
    - 这个过程沿着类的继承链一直往上执行，直到达到继承链的最顶部。
    - 当达到了继承链的最顶部，而且继承链的最后一个类已经确保其所有的存储型属性已赋初值，这个实例的内存被认为已经完全初始化，此时，阶段1完成。
    
    **阶段 2**
    - 从继承链的顶部往下，继承链上每个类的指定构造器都有机会进一步自定义实例。构造器此时可访问 `self` ，修改它的属性并调用实例方法等。
    - 最终，继承链中任意的便利构造器都有机会自定义其实例和使用 `self` 。
    
8. 可失败构造器的参数名和参数类型，不能与其他非可失败构造器的参数名，及参数类型相同。严格来说，构造器都不支持返回值。因为构造器本身的作用，只是确保对象能被正确的构造。因此只是用 `return nil` 来可失败构造器构造失败，而不要用关键字 `return` 来表明构造成功。

    ```
    struct Animal {
        let species: String
        init?(species: String) {
            if species.isEmpty {
                return nil
            }
            self.species = species
        }
    }
    ```
    
    
#### 析构过程

1. 析构器只适用于类类型，析构器是在实例被释放前自动调用的。不能主动调用析构器。子类继承父类的构造器，并且在子类的构造器实现的最后，父类构造器会被自动调用。


#### 可选链

1. 嗯，就是那个 `?`。这里只是说明了怎么调用属性、方法、下标等。


#### 错误处理

1. `Swift` 中的错误处理和其他语言中用 `try` 、 `catch` 、 和 `throws` 进行异常处理很像。和其他语言（包括 `Objective-C`）的异常处理不同的是， `Swift` 中的错误处理不涉及解除调用栈，这是一个计算代价高昂的过程。就此而言， `throw` 语句的性能特性可以和 `return` 语句媲美。


2. 为了表示一个函数、方法或构造器可以抛出错误，在函数声明后加上 `throws` 关键字。一个标有 `throws` 的函数称为 `throwing` 函数。如果这个函数指明了返回类型，`throws` 关键字要写在返回箭头 `->` 的前面。只有 `throwing` 函数可以传递错误。任何在非 `throwing` 函数内部抛出的错误都只能在函数内部处理。
3.  `do-catch` 可以对错误进行匹配。 `defer` 是将代码的执行延迟到当前作用域退出之前。


#### 类型转换

1. 类型转换在 `Swift` 中使用 `as` 和 `is` 操作符实现。
2. 类型检查操作符 `is` 来检查一个实例是否属于特定子类型。 `as?` 或者 `as!` 用来转换。
3. `Swift` 为不确定的类型提供了两种类型别名：
    - `Any` 标识任何类型，包括函数类型。
    - `AnyObject` 可以表示任何类类型的实例。
    

#### 嵌套类型

1. 所谓 **嵌套类型**，是指可以在支持的类型中定义嵌套的枚举、类和结构体。要在一个类型中嵌套另一个类型，将嵌套类型定义在其外部类型的 `{ }` 内即可，而且根据需要可以定义多级嵌套。（根据 `SwiftLint` 不建议嵌套过多）


#### 扩展

1. 扩展可以给一个现有类、结构体、枚举，还有协议添加新的功能。它拥有在不访问扩展类型源码实现的基础上就完成功能扩展的能力，即逆向建模。但需要注意的是，扩展可以添加新的功能，但是不能重写已经存在的功能。`Swift` 中的扩展可以：
    - 添加计算型实力属性和类属性。
    - 定义实例方法和类方法。
    - 提供新的构造器。
    - 定义下标。
    - 定义和使用新的嵌套类型。
    - 使已经存在的类型遵循某个协议。
    

2. 扩展可以添加新的计算属性，但是不能添加存储属性，或着向现有属性添加属性观察者。
3. 扩展可以添加便利构造器，但是不能添加指定构造器。


#### 协议

1. 有时候需要在方法中**改变**（或**异变**）方法所属的实例，对值类型，则需要加 `mutating` 关键字。如果在协议中定义了一个实例方法，该方法会改变遵循该协议的类型的实例，那么在定义协议时需要在方法前添加 `mutating` 关键字。这使得结构体和枚举能够遵循该协议并满足此方法要求。
2. **协议合成**，即多个协议合一通过 `&` 符号结合起来。用的并不是很多。
3. 协议扩展，可以更优雅的实现 `可选协议` 。


#### 泛型

1. 请始终以大写字母开头的驼峰命名法来为类型参数命名，以表明它是一个占位**类型**，而不是一个值。
2. 类型约束指定类型参数必须继承自指定类、遵循特定的协议或协议组合。如下：
    ```
    func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
        // 这里是泛型函数的函数体部分
    }
    ```
3. **关联类型**

    定义一个协议时，声明一个或多个关联类型作为协议定义的一部分将会非常有用。关联类型为协议中某个类型提供了一个占位符名称，其代表的实际类型在协议被遵循时才会指定。关联类型通过  `associatedtype` 关键字来指定。
    ```
    protocol Container {
        associatedtype Item
        mutating func append(_ item: Item)
        var count: Int { get }
        subscript(i: Int) -> Item { get }
    }
    ```
    
    下面用一个结构体来遵循这个协议：
    
    ```
    struct IntStack: Container {
        // IntStack 的原始实现部分
        var items = [Int]()
        mutating func push(_ item: Int) {
            items.append(item)
        }
        mutating func pop() -> Int {
            return items.removeLast()
        }
        // Container 协议的实现部分
        typealias Item = Int
        mutating func append(_ item: Int) {
            self.push(item)
        }
        var count: Int {
            return items.count
        }
        subscript(i: Int) -> Int {
            return items[i]
        }
    }
    ```
    
    或者使用一个泛型 `Stack` 来遵循该协议：
    ```
    struct Stack<Element>: Container {
        // Stack<Element> 的原始实现部分
        var items = [Element]()
        mutating func push(_ item: Element) {
            items.append(item)
        }
        mutating func pop() -> Element {
            return items.removeLast()
        }
        // Container 协议的实现部分
        mutating func append(_ item: Element) {
            self.push(item)
        }
        var count: Int {
            return items.count
        }
        subscript(i: Int) -> Element {
            return items[i]
        }
    }
    ```
    
    也可以在协议里给关联类型添加约束来要求遵循的类型满足协议：
    
    ```
    protocol Container {
        associatedtype Item: Equatable
        mutating func append(_ item: Item)
        var count: Int { get }
        subscript(i: Int) -> Item { get }
    }
    ```
    
    或者在关联类型约束里使用协议：
    
    ```
    protocol SuffixableContainer: Container {
        associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
        func suffix(_ size: Int) -> Suffix
    }
    ```
    
2. ** 泛型 `Where` 语句**

    需要灵活使用 `where` 进行条件约束即可。
    
    
#### 不透明类型

1. 虽然使用不透明类型作为返回值，看起来和返回协议类型非常相似，但两者有个主要区别，就是是否需要保持类型一致性。一个不透明类型只能对应一种具体的类型，即便函数调用者并不知道是哪一种类型。协议类型可以对应多个类型，只要他们都遵循统一协议。总得来说，协议类型更具灵活性，底层类型可以存储更多的值，而不透明类型对这些底层类型有更强的限定性。用法如下：
    ```
    protocol Shape {
        func draw() -> String;
    }

    struct Quare: Shape {
        var size: Int
    
        func draw() -> String {
            return size.description;
        }
    }

    class ViewController: UIViewController {

        override func viewDidLoad() {
            super.viewDidLoad()
        }
    
        /// 返回协议类型
        func makeOneQuare() -> Shape {
            let q1 = Quare(size: 10)
            print(q1.draw())
            return q1;
        }
    
        /// 返回不透明类型
        func makeSecondQuare() -> some Shape {
            let q2 = Quare(size: 10)
            print(q2.draw())
            return q2;
        }
    }
    ```
2. 说一下自己的看法吧：不透明类型是可以被协议类型替代使用的，这个就好像 `weak` 、 `unowned` 两个关键词一样，我们有知道区别的必要性，但是即使只用 `weak`，就可以解决循环引用的问题了。可能真的有只能使用 `unowned` 的情况，如果以后遇到，会在这里补充。 


#### 自动引用计数

1. 引用计数仅仅用于类的实例。结构体和枚举都是值类型，不是引用类型，也不是通过引用的方式存储和传递。
2. **弱引用**不会对其引用的实例保持强引用，因而不会阻止 `ARC` 销毁被引用的实例。这个特性阻止了引用变为循环引用。关键字 `weak`。另外，当 `ARC` 设置弱引用为 `nil` 时，属性观察不会被触发。
3. 和**弱引用**类似，**无主引用**不会牢牢保持引用的实例。和弱引用不同的是，无主引用在其他实例有相同或更长声明周期时使用。关键字是 `unowned` 。无主引用通常都被期望有值。不过 `ARC` 无法再实例被销毁后将无主引用设为 `nil` ，因为非可选类型的实例不允许被置为 `nil`。并且，使用无主引用，必须确保引用始终指向一个未被销毁的实例。如果在该实例被销毁后，访问该实例的无主引用，会触发运行时崩溃。
4. 闭包和类相似，都是引用类型。循环引用可以通过弱引用或者无主引用来解决。


#### 内存安全

##### 1. 内存访问冲突的实质：
 1. 内存访问冲突时，要考虑内存访问上下文的三个性质：访问是读还是写，访问的时长，以及访问的地址。特别是，冲突会发生在有两个访问符合下列情况时：
    - 至少有一个是写访问
    - 他们的访问是同一个内存地址
    - 他们的访问在时间线上有部分的重叠。
    
##### 2. `Swift` 中两种长期访问类型：
- `In-Out` 参数的访问冲突。
- 结构体 `mutating` 方法里。

##### 3. [这个小节](https://swiftgg.gitbook.io/swift/swift-jiao-cheng/25_memory_safety)里举的例子值得回头再看看。

主要是针对长期内存访问的分析，和解决方法。



#### 访问控制

##### 1. 访问级别

`Swift` 为代码中的实体提供了五种不同的访问级别：
- `open` 和 `public` 级别可以让实体被同一模块的所有实体访问。在模块外也可以通过导入该模块来访问原文件里的所有实体。通常情况下，使用 `open` 或者 `public` 来定义模块对外访问接口。
    > `open` 只能作用于类或者类的成员，它和 `public` 的区别主要在于 `open` 限定的类或者类的成员能够在模块外被继承和重写。将类的访问级别显示指定为 `open` 表明已经设计好了类的代码，并且充分考虑到了这个类在其他模块作为父类时的影响。
- `internal` 级别让实体被同一模块源文件的任何实体访问，但是不能被模块外的实体访问。通常情况下，某个实体只在应用程序或者框架内部使用，可以将其设置为 `internal` 级别。同时，这个也是默认访问级别。
- `fileprivate` 限制实体只能在其定义的文件内部访问。如果功能代码的实现细节只需要在文件内部访问时，可以使用 `fileprivate` 来将其隐藏。
- `private` 限制实体只能在其定义的作用域，以及同一文件的 `extension` 中访问。非同一个源文件的 `extension` ，也是无法访问的。

以上， `open` 的访问级别最高，限制最少。`private` 的访问级别最低，限制最多。

##### 2. 访问级别规则

`Swift` 中访问级别遵循一个原则：**实体不能定义在具有更低级别访问的实体中**。例如：
- 一个 `public` 的变量，其所在类型的访问级别不能是 `internal`、`fileprivate` 或者 `private` 。因为无法保证变量的类型在使用变量的地方也具有访问权限。
- 函数的访问级别不能高于它的参数类型和返回类型的访问级别。因为这样就会出现函数可以在任何地方被访问，而参数和返回类型不可以的情况。

注意，即使违反上面的规则，正常情况下，也不会编译或者运行错误…

#### [高级运算符](https://swiftgg.gitbook.io/swift/swift-jiao-cheng/27_advanced_operators)

开发中比较少需要使用高级运算符，这里留着记录，具体使用时在参考，点击标题即可。

##### 1. 位运算符

- 按位取反运算符 `Bitwise NOT Operator` 
    - 按位取反运算符 `~` 对一个数值的全部比特位进行取反
    - 按位取反运算符是一个前缀运算符，直接放在运算数之前，并且他们之间不能添加任何空格。
    ```
    let age = 18
    let invertAge = ~age
    print(invertAge)  // -19
    ```

- 按位与运算符 `Bitwise AND Operator` 
    - 按位与运算符 `&` 对两个数的比特位进行合并。它返回一个新的数。只有当两个数的全部对应为都为 `1` 的时候，新数的对应位才为 `1` 。
    ```
    let one = 10
    let sec = 10
    let third = 20
    let and1 = one & sec
    let and2 = one & third
    print(and1, and2)   // 10         0
    ```
- 按位或运算符 `Bitwise OR Operaor`
    - 按位或运算符 `|` 可以对两个数的比特位进行比较。它返回一个新的数，只要两个数的对应位中任意一个为 `1` ，新数的对应位就为 `1` 。

- 按位异或运算符 `Bitwise XOR Operator` 
    - 按位异或运算符，或*称排外的运算符* `^`，可以对两个数的比特位进行比较。它返回一个新的数，当两个数的对应为不相同时，新数的对应位就为 `1`，对应位相同时，则为 `0` 。

- 按位左移、按位右移运算符 `Bitwise Left Right Shift Operators` 
    - 按位左移运算符 `<<` 和按位右移运算符 `>>` 可以对一个数的所有位指定位数的左移或者右移，但是需要遵循下面定义的规则。
    - 对一个数进行按位左移或者按位右移，相当于对这个数进行乘以 `2` 或者除以 `2` 的运算。将一个整数左移一位，等价于将这个数乘以 `2` 。同样的，将一个整数右移一位，等价于将这个数除以 `2` 。
    - 无符号整数的移位运算（简单）
    - 有符号整数的移位运算（复杂）

##### 2. 溢出运算符
- 溢出加法 `&+`
- 溢出减法 `&-`
- 溢出乘法 `&*`

##### 3. 优先级和结合性

运算时，加上必要的括号，更具可读性。

##### 4. 运算符函数

类和结构体可以为现有的运算符提供自定义的实现，这通常被称为 `运算符重载` 。

- 前缀和后缀运算符，如 `-a` 、`b!` 
- 符合赋值运算符，如 `=` 、 `+=`
- 等价运算符，如 `==` 、 `!=` 

##### 5. 自定义运算符
自定义运算符的优先级。

### 后记
本来是想一星期补完的， 结果断断续续持续了两周多。这里仅仅是记录自己日常中容易忽视的，或者不太常用的一些点。语言参考只是让我们对这个语言有个大概的了解，想要加深对 `Swift` 的理解，还是应该更多的去看优秀的源码，自己动手去实践和总结。