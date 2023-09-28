---
layout: post
title: "Swift 中的协变与逆变"
date: 2023-09-27T22:47:33+08:00
category: Swift
comments: true
---

**协变 - Covaiance**  
**逆变 - Contravariance**   

我们在使用泛型的时候经常会遇到这两个关键字. 
- `__covariant`: 用于泛型数据强转类型，可以向上强转，子类可以转成父类。
    这个比较好理解, 了解过面向对象编程的五个基本原则 SOLID 中的 L(里氏替换) 原则的话, 就不难理解子类可以在任何父类出现的地方替换父类.  
- `__contravariant`: 用于泛型数据强转类型，可以向下强转，父类可以转成子类. 这个就比较难理解了.  

抛开 Swift, 我们先从计算机科学层面来看一下什么是 Variance(变型, 这个是维基百科的翻译, 个人觉得不好理解, 但是我也想不出更好的翻译了, 后续就直接使用英文原词).

首先, 许多编程语言是支持子类型 (subtyping) 系统的, 比如说, 类型 Cat 是 Animal 的子类型, 那么任何使用 Anmial 类型的地方都可以使用 Cat 类型来替换.  

Variance 就是用来描述如何根据组成复杂类型的简单类型之间的子类型关系, 来确定复杂类型之间的子类型关系的. 比如: Cat 数组和 Animal 数组之间是什么样的子类型关系? 或者, 返回 Cat 的函数和返回 Animal 的函数之间是什么样的子类型关系?  

根据类型构造器(type constructor) 的 Variance 不同, 复杂类型可能会保留, 反转或者忽略原来的简单类型之间的子类型关系. 举例说明, Cat 数组是 Animal 数组的子类型, 是因为数组类型构造器是协变的(Covariant). Covariant 意味着复杂类型**保留**了简单类型之间的子类型关系.   

另一个例子, 函数 `Animal -> String`(接收 Animal, 返回 String)是函数 `Cat -> String` 的子类型, 是因为函数类型构造器在参数类型上是逆变的(Contravariant). Contravariant 意味着复杂类型**反转**了简单类型之间的子类型关系.  

编程语言的设计者在指定数组, 继承, 泛型等类型规则的时候, 必须要考虑到 Variance. 将类型构造器设计成是协变(covariant)、逆变(contravariant)而不是不变的(invariant)，可以让更多的程序具备良好的类型。 

对于编程者来说, 经常会感到 contravariance 是反直觉的. 为了保持类型系统简单和利于编程, 一个编程语言可能把类型构造器视为不变的，即使它被视为可变也是安全的；或是把类型构造器视为协变的，即使这样可能会违反类型安全.

# Varinance 的正式定义
假定 A 和 B 是两个简单类型, T\<U\>表示一个类型构造器 I 应用于类型参数 U. 在编程语言的类型系统中, 一个类型构造器 T 的类型规则是:
- **协变 (Covariant)**, 保留简单类型的关系. 如果 `A ≤ B`, 那么 `T<A> ≤ T<B>`;
- **逆变 (Contravariant)**, 反转简单类型的关系. 如果 `A ≤ B`, 那么 `T<B> ≤ T<A>`;
- **双变 (Bivariant**), 既协变又逆变. 如果 `A ≤ B`, 那么 `T<A> ≡ T<B>`;
- **Variant**, 如果存在上述但中变化中的任一种(Convatiant, Contravariant or Bivarant), 那么就是 Variant;
- **不变 (Invariant or Nonvariant)**, !Variant


# Swift 中的 Convariance 和 Contravariance  
下面的这段代码是会报错的

``` Swift
var intHandler: (Int) -> Void = { (num) in 
    print(num)
}
let anyHandler: (Any) -> Void = intHandler **___ ERROR\!**
```  
但是反过来就不会报错
``` Swift
let anyHandler: (Any) -> Void = { (any) in
    print(any)
}
let intHandler: (Int) -> Void = anyHandler ___ OK.
```
然后, 如果是这样将 Closure 用作另一个 Closure 的参数, 再赋值, 也不会报错
``` Swift
let intResolverLater: ((Int) -> Void) -> Void = { f in
    f(0)
}

var anyResolverLater: ((Any) -> Void) -> Void = intResolver ___ OK.
```

结合上面的 Covariant 和 Contravariant的介绍, 如果你清楚上面的代码报错和不报错的原因, 那就不需要再往下看了. 但是如果你好奇的话, 可以继续阅读.  

我们知道子类和用在任何父类出现的地方.  
``` Swift
class Animal { ... }
class Cat: Animal { ... }
let animal: Animal = Cat()
```
这种行为叫做子类型化, Cat 是 Animal 的子类, Animal 是 Cat 的父类.  

简单类型的子父类关系很好判断, 现在我们想一下复杂类型的子父类关系.  

- Array —  `[Cat]` 是 `[Animal]` 的子类型不?
- Generic — `PetOwner<Cat>` 是 `PetOwner<Animal>` 的子类型不?
- Closure — `(Cat) -> Void` 是 `(Animal) -> Void` 的子类型不?  

答案: 第一个是, 第二个不是(这个后续在解释), 第三个不是.  

实际上, 第三个恰恰相反, **`(Animal) -> Void` 是 `(Cat) -> Void` 的子类型!**

这不是语言的黑魔法, 只是语言在设计时处理它们的一个合理的选择, 我们之需要记住这种选择就可以. 这种选择就是 协变(covariance)和逆变(contravariance).

# 什么是协变 Covariance
仔细分析一下为什么`[Cat]`是`[Animal]`的子类型.  
我们使用箭头指向表示 Cat 是 Animal 的子类型:  
``` plain
Cat → Animal
```
认真思考一下, [Animal] 中的元素既可以是 Animal, 也可以是 Cat. 所以, 语言的设计者就可以决定将 [Cat] 视为 [Animal] 的子类型. 用箭头表示子类型关系就是:
```
[Cat] → [Animal]
```
[Cat] 和 [Animal] 之间的子类型关系的方向是和组成它们的简单类型 Cat 以及 Animal 之间的子类型关系的方向相同的. 这种使用和简单类型(或者叫原始类型)类型关系相同的决定叫做协变(covariance).  

协变(covariance)的另一个例子是闭包的返回类型:

``` Swift
let intBuilder: () -> Int = {
    return 5
}
let anyBuilder: () -> Any = intBuilder ___ OK
```
我们可以看到 `Int` 是 `Any` 的子类型, `() -> Int` 同样是 `() -> Any` 的子类型. 所以, 闭包的返回类型在 Swift 中是协变的(covariant).

# 什么是逆变 "Contra"variance( contra 是指相反的意思, 这里可以理解成相反的变化)
逆变(Contravariance) 就是将原始类型的子类型关系反转的一种决定.  

我们通过**闭包的参数**来分析一下,为什么这种子类型关系的反转是合理的. 假定下面的代码可以正常运行:
``` Swift
let intHandler: (Int) -> Void = { num in
    print(num)
}
let anyHandler: (Any) -> Void = intHandler ___ COMPILE ERROR!
```
想像一下, 当执行这条语句时, 会发生什么?
``` Swift
anyHandler("Some String")
```
`intHandler` 会接收到一个意料之外的 `Stirng` 类选的参数. 当然也可能是任何除了`Int`类型以外的参数. `intHandler`不知道如何处理非`Int`类型以外的参数. 所以, 这段代码在编译时就会报错.  

现在, 我们再想想反过来会如何:
``` Swift
let anyHandler: (Any) -> Void = { (any) in
    print(any)
}
let intHandler: (Int) -> Void = anyHandler ___ OK.
```
这样看起来就合理了. 因为我们只能给`intHandler`提供一个`Int`类型的参数, `anyHandler`可以处理任意类型的参数,包含`Int`类型. 
``` Swift
intHandler(1001)
```
所以 `anyHandler` 是 `intHandler` 的子类型. 这就意味着, 任何出现 `anyHandler` 的地方都可以使用 `intHandler`来代替.  
这种闭包的类型方向和原始的闭包参数的类型方向相反, 叫做逆变(contravariance).

# 接下来,我们就可以来分析一下第三段代码为什么不会报错
``` Swift
let intResolverLater: ((Int) -> Void) -> Void = { f in
    f(0)
}

var anyResolverLater: ((Any) -> Void) -> Void = intResolver ___ OK.
```

1. 首先, 下面的这个关系, 我们已经非常熟悉了(此处省略变量名)
``` Swift
let Any = Int
```
2. 然后, 对于一个函数来说, 左侧的 `Int` 类型的参数应该是右侧 `Any` 类型的参数的子类型. 
``` Swift
let (Int) -> Void = (Any) -> Void
```
可能不是很明显(逆变), 这同时也表明了 `(Any) -> Void` 是 `(Int) -> Void` 的子类型.  
3. 最后, 使用同样的逻辑, 左侧的参数 `((Any) -> Void)`应该是 右侧的参数 `((Int) -> Void)` 的子类型. 证明过程同第 2 步.(又一次逆变) 
``` Swift
let ((Any) -> Void) -> Void = ((Int) -> Void) -> Void
```
类型关系的方向被不断的被交换.  

通过一个用例, 尝试来看一下这段代码是怎么正确执行的
``` Swift
let intResolverLater: ((Int) -> Void) -> Void = { (f) in
   // Use f to handle some Int
   f(1000)
}
let anyResolverLater: ((Any) -> Void) -> Void = intResolverLater
// anyResolver must be able to handle Any (can possibly be Int)
let anyResolver: (Any) -> Void = { (any) in
   switch any {
   case num as Int:
      print("Got an int! \(num)")
   ...handle other cases
   }
}
// anyResolver can be used to handle Any (or Int) safely later!
anyResolverLater(anyResolver)
```

# 一个可视的帮助判断的方法  

我们可以把闭包和函数的子类化行为想像成一根水管. `f: (A) -> B` 是一根水管, 它的输入是 A, 输出是 B, 两端和系统的其它部分是相匹配的, 这样里面的水可以顺利的流过.  

![f: (A) — > B](/images/pipe-1.webp)  

如果想要在保证水能安全的流过的前提下来替换这根水管, 那么新的水管就比要有更大的进水口(A 的父类型)和更小的出水口(B 的子类型).

![f: (A) — > B](/images/pipe-2.webp)  

如图所示, 新水管 `f′` 可以用在任何原来的水管 `f` 使用的地方, 但是反过来不行. 所以, 闭包 `f′` 是闭包 `f` 的子类型.

# 不变性,不相关性 (Invariance)  
`Int` 和 `String` 是不相关的(invariance). 它们的了类型不兼容. 互相之间不能替换.  
Swift 中的泛型 (Generic) 是不变性 (invariance). 这意味着 `PetOwner<Cat>` 不是 `PetOwner<Animal>` 的子类型. 它们之间互相没有关系...

# 最后
最后, 让我们用一个小坑来结束. 为什么 Swift 标准库中的泛型, 比如说 `Array<Animal>` 是协变的(convatiant) 但是, 我们自己定义的泛型, (比如说`PetOwner<Animal>`) 确是 不变性(invariant) 的? 

这么看来, 这背后应该是有一些神奇的魔法. [Rick](https://www.mikeash.com/pyblog/friday-qa-2015-11-20-covariance-and-contravariance.html#comment-e1defaef8e71a2dcc471b51f67725737)是这样说的:
>“Swift generics are normally invariant, but the Swift standard library collection types — even though those types appear to be regular generic types — use some sort of magic inaccessible to mere mortals that lets them be covariant.”

# Reference 
[WIKIPEDIA-Convariance and Contravariance](https://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science))  
[Convariance and Contravariance in Swift](https://medium.com/@aunnnn/covariance-and-contravariance-in-swift-32f3be8610b9)  
[Friday Q&A 2015-11-20: Covariance and Contravariance
by Mike Ash ](https://www.mikeash.com/pyblog/friday-qa-2015-11-20-covariance-and-contravariance.html)