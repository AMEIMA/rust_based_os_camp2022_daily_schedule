# 2022年开源操作系统训练营日记
## 2022/7/4
今天是加入训练营的第一天，做了Rust-lang Lab Test based on Rustlings前65个训练，尽量不看rustc的错误提示直接分析代码。

做题的过程中发现了一些之前没有关注到的知识：

1. variables6：常量必须手动显示指定类型
2. function2：程序项不必前置声明
3. if2：rust中并没有elif这种else if的缩写
4. move_semantics1：Vec要加上mut才能改动
5. move_semantics2：使用clone()可以深拷贝
6. move_semantics5：一个变量同时只能有一个可变引用 如果定义了第二个可变引用 会掩盖掉第一个可变引用
7. move_semantics6：&data.to_uppercase()指的是&(data.to_uppercase())而非(&data).to_uppercase()，把一个引用data给了to_uppercase()，这个引用data就已经挂掉了，不能再用data = &data.to_uppercase();这种形式改变值。函数声明中的mut是写在变量名前的而不是类型名前。
8. primitive_types3：不能用let a = [u8; 100];这种形式创建数组，必须要给一个初始值像let a = [0; 100];这样。如果用let a: [u8; 100];则会报错尝试使用未初始化的数据，连读取len都是不允许的。
9. primitive_types4：切片时必须要前面加一个&，对a进行切片以后，使用其中的部分数据，不应该去获取其部分数据的所有权，因为a在析构时会作为整体析构，如果不加&会造成双释放。（显式的优先级同样也是&(a[1..=3])，看来&符号的优先级很低啊）
10. structs1：结构体声明大括号后不跟分号。要注意&str和String的区别。涉及到引用的结构体需要使用显示生命周期标注，标注格式是<'a>在结构体名后而非structs后，字段标注是放在&符号后而不是前面，即&'a str。
11. structs2：结构体更新语法let 新实例 = 结构体名 {额外覆盖的字段名, ..旧实例}，无需考虑顺序。如果有使用旧实例中非copy的字段，则会发生move，造成整个旧实例不可用。..旧实例后不能跟逗号。
12. structs3：使用panic!()可以产生panic。&self 实际上是 self: &Self 的缩写，所以在方法中使用的时候无需再加&就已经是&Self类型的了。
13. enum2：带类型的枚举并不使用冒号，而是直接在枚举项后面接 {} ()。可以用“=”指定索引（isize）
14. enum3：match的各匹配分支以逗号而非分号结尾。.符号用来索引字段，或是使用方法（第一个参数使用了self关键字的）。定义带类型的枚举项时可以使用()来包裹结构体，免去匹配时拆包的麻烦。&mut self表示self: &mut Self。Rust中没有“形参1, 形参2: 它们共同的类型”这种缩写方式。
15. hashmap2：使用basket.entry(fruit).or_insert(5);形式在不存在键时插入。
16. quiz2：to_owned指从借用的数据创建拥有的数据，通常是通过克隆，返回string。Into把当前类型转换为另一种类型，所以有into()的那一行是两种都可以。format返回string，trim返回&str，replace返回string，to_lowercase返回string。
17. error1：Option<T>是个枚举，有两种枚举类型，Some()和None。Result<T, E>也是枚举，有Ok(T)和Err(E)。这些泛型约束都是sized，也就是基本没有要求。函数签名出现引用，要标记生命周期。
18. error5：?会在出错时提前返回出错的函数签名中给出的错误类型，而非调用处所处函数签名中的类型。match中可以用“x if x < 0”这种形式限制匹配。动态错误类型Box<dyn error::Error>
19. error6：使用map_err可以进行错误转换。利用编写错误转换函数的方式进行转换。

## 2022/7/5
今天继续完成了Rust-lang Lab Test based on Rustlings剩下的19个训练，涉及类型转换还有错误处理的地方还是蛮难的，接下来要针对性的学一下这些地方。除此之外今天提前看了看《计算机组成与设计（RISC-V版）》一、二章。

今天做题找到的一些薄弱点：

1. iterators2：三种迭代iter()、iter_mut()、into_iter()。
2. iterators3：map迭代适配器，将某个迭代器转换成另一种迭代器。collect()会把包含Result的向量生成的迭代器收集成Result包含的向量，可以使用collect::<Vec<Result<i32, DivisionError>>>()来指定收集的结果类型。
3. iterators4：(1..=num).product()可算阶乘。发现我对标准库中的iter了解还是太少了。
4. iterators5：map可以嵌套着使用，不确定是不是有更好的写法，不过暂时写的collection.iter().map(|c| c.values().map(|v| {if v == &value { 1 } else { 0 }}).sum::<usize>()).sum()通过了。
5. threads1：线程还有内部可变性的知识还需要加强。
6. macros2：宏不能先只声明，在后面再定义。
7. macros3：宏匹配分支间由;分隔。
8. quiz4：才知道字符串拼接可以直接用 + 或者format!，用了好久的push_str。。。
9. try_from_into：区间可以用contains()来判断元素是否在其中。
10. as_ref_mut：转换方面我也不是很熟。这里要求在函数签名中限制泛型为可以转换为str才行，因为要用到.as_ref().chars()和.as_bytes().len()，所以增加<T: AsRef<str>>。
11. advanced_errs1：convert确实要好好看一下了。实现 AsRef trait 以实现廉价的引用到引用转换、实现 AsMut trait 进行廉价的变量到变量转换、实现 From trait 以进行值到值的转换、实现 Into trait，以便将值转换为当前 crate 之外的类型。

## 2022/7/6
做完了Rustlings，有了一些实际操作的经验，趁现在想再重新回去把参考手册、标准库从头到尾看一看。

今天只看了参考手册的前面一部分，对新的感受做一下简单记录：

1. Rust使用utf-8源代码文件
2. XID_start比XID_continue排除掉了数字下划线之类的字符，说明rust的标识符不能用这些开头（除了_）
3. 原生标识符r#可以使用关键字，并且r#不会被识别进表示符中
4. 关键字算是被保留的编译器内置标识符。
5. 文档注释相关：
    1) 加了!符号的宏或者文档注释，都是针对整个文件而非后面一个程序项的。
    2) rust有个rustdoc工具，接受crate或markdown文件作为参数，生成web格式的文档。在 crate 根目录执行rustdoc src/lib.rs --crate-name docs或cargo doc可以生成网站结构的新目录doc，rustdoc 默认只会为公共函数生成文档。outer文档注释会和程序项一起出现。inner大概就相当于直接插入在页面的对应位置了，example代码什么的也都是直接以文本放进去的。cargo目前不能从md文件生成文档，但rustdoc可以。
    3) rust中使用的markdown规范：https://commonmark.org/help/、https://spec.commonmark.org/0.30/
    4) 星号或下划线用于粗体或斜体，反斜杠可以用于转义或不换段换行，块使用>，链接使用<>或[]()或[][][]:，图片使用![]，代码块可以inline。除此之外，扩展了波浪号删除线，[^]角标，- [x] 任务列表，和github一样的表格表示
    5) /// 文档注释内容 就是 #[doc = "文档注释内容"] 的语法糖，![doc()]可以对文档总体进行一些设置。
    6) 注释代码块中的行前加 # 可以隐藏
    7) Rustdoc 能够使用 item 的路径直接内部链接[]
6. 字符串前面加b后可以使用全部的ascii，叫做字节串，长度为 n 的字节串字面量类型为 &'static [u8; n]。
7. 字面量可以加后缀，能被宏识别并被编译器去除。
8. 浮点数可以用1_234.0E+18f64这种格式。
9. token也就只有关键字、标识符、字面量、生存期、标点符号、分隔符六种。大概注释和空白符在词法解析阶段就被排除出去了，所以不是token。
10. ||和&&是短路的。

## 2022/7/7

今天主要继续看参考手册。顺便看了看riscv的RISC-V手册，发现这本书我很久之前看过一遍，目前对特权级和MMU的原理还不是很透彻，之后再多找点材料看一看。

今天的参考手册杂记：
1. 匹配器和转码器都必须由定界符(delimiter)包围。宏可以扩展为表达式、语句、程序项（包括 trait、impl 和外来程序项）、类型或模式。
2. 加\\$的是元变量，可以匹配指定类型的token。不加\$的按原样进行匹配。匹配器的外层定界符将匹配任何一对定界符。
3. tt，token tree，由token和定界符组成的递归树状结构。
4. 除了标识符、生命周期、token树以外的，宏匹配转发时均对被调用的宏不透明。
5. 重复元用\\$()表示，可以用*、+、?限定数量，之间可放置token作为分隔符。不带\$的是定界符。转码器中也得出现同样数量限定才行。
6. 宏使用#[macro_export]作用于宏定义则会将宏置于crate顶部。使用#[macro_use()]作用于extern crate上时可以导入宏。
7. 默认情况下，宏中引用的所有标识符都按原样展开，并在宏的调用位置上去查找。在路径的开头使用元变量 \\$crate，可以强制在定义宏的 crate 中进行查找。（但仍会受可见性限制）
8. 过程宏必须在 crate 类型为 proc-macro 的 crate 中定义，需要在toml中开启，proc-macro = true。
9. 过程宏接受Token流，生成Token流。
10. 一般编写过程宏还会用到syn和quote包。
11. crate 是编译和链接的单元，也是版本控制、版本分发和运行时加载的基本单元。
12. #![crate_name = ""]可指定crate名。
13. 使用cfg进行条件编译。分为三种形式：#[cfg(all(unix, target_pointer_width = "32"))]条件编译、#[cfg_attr(feature = "magic", sparkles, crackles)]条件展开属性、cfg!(unix)条件计算表达式。
14. 目标三元组可以理解为我们常说的平台信息，包含这些信息：第一项元素：CPU 架构；第二项元素：供应商；第三项元素：操作系统；第四项元素：ABI。
15. 任意配置集的配置选项可以使用命令行参数 --cfg 来设置。
16. 处在函数等程序项内的程序项，在模块的命名空间中的路径名由封闭它的程序项的名称限定。
17. extern crate可出现在各层的作用域中。
18. 可以在外部项(extern crate item)上指定使用 no_link属性，以防止此 crate 被链接到编译输出中。这通常用于加载一个 crate 而只访问它的宏。
19. 通过使用形如为 use path as _ 的带下划线的 use声明，可以在不绑定名称的情况下导入程序项。这对于导入一个 trait 特别有用，这样就可以在不导入 trait 的 symbol 的情况下使用这个 trait 的方法。

## 2022/7/8

继续看参考手册。今天对源代码结构、位置与值表达式、类型与泛型系统的关系还有内存管理有了更深层次的理解。

今天的杂记：
1. 从 2018 版开始，use声明可以像 extern crate 一样指定外部 crate 依赖关系。如果本地程序项与外部的 crate 名称相同，那么使用该 crate 名称需要一个前导的 :: 来明确地选择该 crate 名称。
2. 任何在 let绑定中有效的模式都可以有效应用在函数参数上。
3. 外部函数限定符(extern)可以提供那些能通过特定 ABI 才能调用的函数的定义。
4. 这些通常与提供了函数声明的外部块程序项一起使用，这样就可以调用此函数而不必同时提供此函数的定义。
5. 声明一个既异步又非安全(unsafe)的函数是合法的。调用这样的函数是非安全的，并且（像任何异步函数一样）会返回一个 future。这个 future 只是一个普通的 future，因此“await”它不需要一个 unsafe 的上下文。
6. 异步函数在调用时不起作用：相反，它们将参数捕获进一个 future。当该函数被轮询(polled)时，该 future 将执行该函数的函数体。
7. 类型别名用关键字 type 声明。每个值都是一个唯一的特定的类型，但是可以实现几个不同的 trait，或者兼容几个不同的类型约束。
8. 枚举构造器可以带有具名字段（结构体）或未具名字段（元组），如果枚举的任何变体都没有附加字段，则可以直接设置和访问判别值。
9. 当一个联合体被销毁时，它无法知道需要销毁它的哪些字段。因此，所有联合体的字段都必须实现 Copy trait 或被包装进 ManuallyDrop<_>。这确保了联合体在超出作用域时不需要销毁任何内容。与结构体和枚举一样，联合体也可以通过 impl Drop 手动定义被销毁时的具体动作。
10. 如果联合体的一个字段是被出借，那么在相同的生存期内它的所有其他字段也都处于出借状态。
11. 常量项是一个可选的具名 常量值，它与程序中的具体内存位置没有关联。无论常量在哪里使用，它们本质上都是内联的，这意味着当它们被使用时，都是直接被拷贝到相关的上下文中来使用的。静态项类似于常量项，除了它在程序中表示一个精确的内存位置。所有对静态项的引用都指向相同的内存位置。静态项拥有 'static 生存期，它比 Rust 程序中的所有其他生存期都要长。静态项不会在程序结束时调用析构动作 drop。
12. 读取或写入可变静态项变量时需要引入非安全(unsafe)块。
13. 孤儿规则，可以防止导入某个库的时候破坏其它库的功能：（1）如果要实现外部定义的 trait 需要先将其导入作用域；（2）不允许对外部类型实现外部 trait；（3）可以对外部类型实现自定义的 trait；（4）可以对自定义类型上实现外部 trait。
11. link属性为外部(extern)块中的程序项指定编译器应该链接的本地库的名称。kind键是一个可选值，它指定具有以下可选值的库类型：dylib、static、framework。
12. 可以有如fn foo<const N: usize>(arr: [i32; N])的方式使用常量参数。常量只能作为类型或数组定义中的重复表达式中的独立实参出现。
13. 所有的程序项声明都可接受外部属性，同时外部块、函数、实现和模块都可接受内部属性。
14. 大多数语句都可接受外部属性（参见表达式属性，了解表达式语句的限制）。
15. 属性要么是活跃的，要么是惰性的。在属性处理过程中，活跃属性将自己从它们所在的对象上移除，而惰性属性依然保持原位置不变。宏属性是活跃的。所有其他属性都是惰性的。
16. 编译器可能允许和具体外部工具相关联的属性，但这些工具在编译和检查过程中必须存在并驻留在编译器提供的工具类预导入包下对应的命名空间中（才能让这些属性生效）。这种属性的（命名空间）路径的第一段是工具的名称，后跟一个或多个工具自己解释的附加段。
17. 全部内置属性：https://rustwiki.org/zh-CN/reference/attributes.html 最下方。
18. lint项由rustc内置提供。也可以将lint级别属性与clippy结合使用。
19. cold属性和 inline属性给出了某种代码生成方式的提示建议，这种方式可能比没有此提示时更快。
20. target_feature[属性] 可应用于非安全(unsafe)函数上，用来为特定的平台架构特性(platform architecture features)启用该函数的代码生成功能。
21. 语句一定是被包裹在块中的，只是在只使用副作用的函数或重新排序分段计算表达式时使用，而块一定是被更外层的表达式或者函数包裹。那看起来rust程序源代码粗略来说有这种结构workspace(package(crate(mod(fn(expr(stmt))))))。工作空间有一个toml，可以包含多个package；package可以包含一个库crate和多个二进制crate；crate可以由树状的mod组成，crate最外层有一个匿名的mod作为根；mod中可以包含各种程序项，mod也是程序项，程序项在一定程度上是层次化嵌套的；程序项中的fn，其定义部分的表达式块中可以包含语句和表达式。
22. 程序项声明语句不会隐式捕获包含它的函数的泛型参数、参数和局部变量。
23. 声明的名称可以表示新变量或新的程序项。
24. 由于表达式是递归执行的，那这些表达式也会从最内层到最外层逐层求值，忽略兄弟表达式，直到没有（未求值的）内部子表达式为止。
25. 表达式分为两大类：位置表达式和值表达式（大概是因为编译期和运行期的区别，只有编译期的语言貌似是不需要位置表达式的。正因为有了两个时期的区别才有了位置表达式来处理在编译期不能计算出的数据。）。位置表达式是表示内存位置的表达式。语言中表现为指向局部变量、静态变量、解引用(*expr)、数组索引表达式(expr[expr])、字段引用(expr.f)、圆括号括起来的位置表达式的路径。所有其他形式的表达式都是值表达式。值表达式是代表实际值的表达式。历史上，位置表达式被称为 左值/lvalues，值表达式被称为 右值/rvalues。在每个表达式中，操作数可以出现在位置上下文，也可出现在值上下文中。表达式的求值既依赖于它自己的类别，也依赖于它所在的上下文。
26. 值表达式用于位置操作数上下文中时，创建一个临时变量把值放进去，把地址返回；位置表达式用于值操作数上下文中时，会从位置中取出值来使用，如果值是个指向另一位置的引用，且非Copy类型，则会发生移动。
27. 块总是值表达式，并会在值表达式上下文中对最后的那个操作数进行求值。
28. 匹配臂可以接受*匹配守卫(Pattern guard)*来进一步改进匹配标准。 模式守卫出现在模式的后面，由关键字 if 后面的布尔类型表达式组成。
29. 等待(await)表达式挂起当前计算，直到给定的 future 准备好生成值。
30. 当一个模式有可能在运行期与它所匹配的值不匹配时，我们就说它是可反驳型的(refutable)。也就是说，*不可反驳型(irrefutable)*模式必须在运行期与它们所匹配的值匹配成功，不然不给编译。
31. Rust 程序中的每个变量、程序项和值都有一个类型。值的类型定义了该如何解释用于保存它的内存数据，以及定义了可以对该值执行的操作。也就是说，类型比泛型多了与内存相关的信息（类型布局），泛型只需要考虑可以执行什么操作，这也是为什么编译语言都要确定类型而不是统一使用泛型，因为需要做内存管理，需要把概念上层次化的“泛型”归约到编译器内置的已知确定的内存大小的“类型”上。不分编译期和运行期的语言大概可以只用泛型系统。
32. trait类型分为trait对象和实现trait，这俩是一对，刚好对应动态分发dyn stait和静态分发impl trait。
33. 在某些情况下，类型组合在一起时可能会产生二义性。此时可以在类型周围使用元括号来避免歧义。
34. 标称类型(nominal types) — 结构体(struct)、枚举(enum)和联合体(union) — 可以是递归的。递归类型必须在递归中包含一个标称类型。也就是说，每个枚举(enum)变体或结构体(struct)或联合体(union)的字段可以直接或间接地指向此枚举(enum)或结构体(struct)类型本身。递归类型的尺寸必须是有限的；也就是说，类型的递归字段必须是指针类型。
35. str类型的值的表示方法与 [u8] 相同，它是一个 8-bit 无符号字节类型的切片。由于 str 是一个动态尺寸类型，所以它只能通过指针类型实例化，比如 &str。要使用切片类型，通常必须放在指针后面使用。
36. 当被引用函数项、元组结构体的构造函数或枚举变体的构造函数时，会产生它们的*函数项类型(function item type)*的零尺寸的值。
37. 闭包类型近似相当于包含捕获变量的结构体。
38. 编译器倾向于优先通过不可变借用(immutable borrow)来捕获闭合变量(closed-over variable)，其次是通过唯一不可变借用(unique immutable borrow)，再其次可变借用(mutable borrow)，最后使用移动语义(move)来捕获。编译器将选择这些中的第一个能让此闭包编译通过的选项。如果使用了关键字 move ，那么所有捕获都是通过移动(move)语义进行的。
39. 闭包类型都实现了 FnOnce，这表明它们可以通过消耗掉闭包的所有权来调用执行它一次。此外，一些闭包实现了更具体的调用trait：对没采用移动语义来捕获任何变量的闭包都实现了 FnMut，这表明该闭包可以通过可变引用来调用。对捕获的变量没移出其值，也没去修改其值的闭包都实现了 Fn，这表明该闭包可以通过共享引用来调用。非捕获闭包(Non-capturing closures)*是指不捕获环境中的任何变量的闭包。它们可以通过匹配签名的方式被自动强转成函数指针（例如 fn()）。
40. std::cell::UnsafeCell<T>类型是 Rust 中唯一可以合法失效多不可变限制的方法。RefCell等实现于其上。
41. Rust 中的子类型化的适用范围非常有限，仅出现在和生存期(lifetimes)的型变(variance)相关的地方，以及那些和高阶生存期相关的类型型变之间。 如果我们擦除了类型的生存期，那么唯一的子类型化就只是类型相等(type equality)了。
42. for<>语法称为高阶特征约束Higher-Rank Trait Bounds(HRTB)，可以对作为参数的传递的闭包的参数进行生命周期标记。
43. 类型构造器中为泛型指定具体类型后会发生型变，扩大是协变、缩小是逆变、不变是不变。
44. 当一个初始化了的变量或临时变量超出作用域时，其析构函数(destructor)将运行，或者说它将被销毁(dropped)。此外赋值操作也会运行其左操作数的析构函数（如果它已经初始化了）。

## 2022/7/9

今天把参考手册看完了，之后继续看根据例子学Rust。

参考手册部分杂记：

1. 标准库中的某些类型和 trait 在 Rust 编译器中也直接能用（编译器内置？）。
2. 一个循环表达式可以选择设置一个标签。 这类标签被标记为循环表达式之前的生存期（标签）。如果循环存在标签，则嵌套在该循环中的带此标签的 break表达式和 continue表达式可以退出此标签标记的循环层或将控制流返回至此标签标记的循环层的头部。
3. *实体(entity)*是一种语言结构，在源程序中可以以某种方式被引用，通常是通过[路径(path)][paths]。实体包括类型、程序项、泛型参数、变量绑定、循环标签、生存期、字段、属性和各种lints。声明(declaration)是一种句法结构，它可以引入名称来引用实体。路径用于引用实体，该引用的实体可以在其他的作用域内。
4. no_implicit_prelude属性*可以应用在 crate级别或模块上，用以指示它不应该自动将标准库预导入包、外部预导入包或工具类预导入包引入到当前模块或其任何子模块的作用域中。
5. 表达式中的路径允许指定带有泛型参数的路径。它们在各种表达式和模式中都有使用。token :: 必须在泛型参数的左尖括号（<）的前面，以避免和小于号操作符产生混淆。这就是俗称的“涡轮鱼(turbofish)”句法。泛型参数的顺序被限制为生存期参数，然后是类型参数，然后是常量参数，再后是相应的约束。常量实参必须用花括号括起来，除非它们是字面量或单段路径。
6. 完全限定性路径可以用来为 trait实现(trait implementations)消除路径歧义。
7. 以 :: 开头的路径被解析为外部预导入包中的一个 crate。
8. super 在路径中被解析为父模块。它只能用于路径的前导段，可以置于 self 路径段之后。super 可以在第一个 super 或 self 之后重复多次，以引用祖先模块。
9. 对于裸实现(bare implementations)来说，它里面的程序项的路径前缀是：它当前实现的程序项的规范路径，再用尖括号（<>）括把此路径括起来。对于 trait实现来说，它内部的程序项的路径前缀是：当前实现的程序项的规范路径后跟一个 as，再后跟 trait 本身的规范路径，然后整个使用尖括号（<>）括起来。
10. 除了公有和私有之外，Rust 还允许用户（用关键字 pub ）声明仅在给定作用域内可见的程序项。
11. Rust 还没有明确的内存模型。
12. 变量是栈帧里的一个组件，可以是具名函数参数、匿名的临时变量或具名局部变量。
13. *常量函数(const fn)*可以在常量上下文中调用。给一个函数加一个常量(const)标志对该函数的任何现有的使用都没有影响，它只限制参数和返回可以使用的类型，并防止在这两个位置上使用不被允许的表达式类型。程序员可以自由地用常量函数去做任何用常规函数能做的事情。
14. used属性只能用在静态(static)项上。此属性强制编译器将该变量保留在输出对象文件中(.o、.rlib 等，不包括最终的二进制文件)，即使该变量没有被 crate 中的任何其他项使用或引用。注意，链接器(linker)仍有权移除此类变量。
15. no_mangle属性来禁用标准名称符号名混淆(standard symbol name mangling)。link_section属性指定了输出对象文件中函数或静态项的内容将被放置到的节点位置。export_name属性指定将在函数或静态项上导出的符号的名称。

根据例子学Rust部分杂记：

1. 块注释可以在表达式中间使用，如：let x = 5 + /* 90 + */ 5;
2. format!：将格式化文本写到字符串。eprint!：与 print! 类似，但将文本输出到标准错误（io::stderr）。
3. 格式化输出可以使用位置参数{0}。可以使用命名参数{subject}。可以在 `:` 后面指定特殊的格式，需要实现不同的trait。
4. 所有类型都能推导（derive，即自动创建）fmt::Debug 的实现。但是 fmt::Display 需要手动实现。
5. 通过 {:#?} 提供了 “美化打印” 的功能。
6. 实现fmt::Display::fmt的时候可以使用`write!`输出fmt::Result，`write!` 的用法和 `println!` 很相似。
7. write! 上使用 ? 会对 `write!` 进行尝试（try），观察是否出错。若发生错误，返回相应的错误。否则（没有出错）继续执行后面的语句。
8. 格式化输出可以使用 :02 补零使位数为 2 位。
9. 尽管单元类型的值是个元组，它却并不被认为是复合类型，因为并不包含多个值。
10. 非引用不会自动转换到引用。（但是调用方法的点运算符会自动帮忙添加&）Rust 也不提供原生类型之间的隐式类型转换（coercion），但可以使用 as 关键字进行显 式类型转换（casting）。
11. self属于别名。
12. enum 的一个常见用法就是创建Cons(u32, Box<List>)链表。
13. 可以先声明（declare）变量绑定，后面才将它们初始化（initialize）。但是这种做法很 少用，因为这样可能导致使用未初始化的变量。编译器禁止使用未经初始化的变量，因为这会产生未定义行为（undefined behavior）。
14. 当把任何类型转换为无符号类型 T 时，会不断加上或减去 (std::T::MAX + 1)直到值位于新类型 T 的范围内。事实上的处理方式是：从最低有效位（LSB，least significant bits）开始保留8 位，然后剩余位置，直到最高有效位（MSB，most significant bit）都被抛弃。当转换到有符号类型时，（位操作的）结果就和 “先转换到对应的无符号类型，如果 MSB 是 1，则该值为负” 是一样的。当然如果数值已经在目标类型的范围内，就直接把它放进去。128 转成 u8 还是 128，但转到 i8 相当于给 128 取八位的二进制补码，会变成-128。
15. `size_of_val` 返回一个变量所占的字节数。
16. 类型别名*并不能*提供额外的类型安全，因为别名*并不是*新的类型。
17. Rust 使用 trait 解决类型之间的转换问题。最一般的转换会用到 From 和 Into 两个 trait。
18. From 和 Into 两个 trait 是内部相关联的，实际上这是它们实现的一部分。如果我们能够从类型 B 得到类型 A，那么很容易相信我们也能把类型 B 转换为类型 A。From<T> trait 允许一种类型定义 “怎么根据另一种类型生成自己”，因此它提供了一种类型转换的简单机制。在标准库中有无数 From 的实现，规定原生类型及其他常见类型的转换功能。TryFrom 和 TryInto trait 用于易出错的转换，也正因如此，其返回值是 Result 型。
19. 要把任何类型转换成 String，只需要实现那个类型的 ToString trait。然而不要直接这么做，您应该实现fmt::Display trait，它会自动提供 ToString。只要对目标类型实现了 FromStr trait，就可以用 parse 把字符串转换成目标类型。
20. loop 有个用途是尝试一个操作直到成功为止。若操作返回一个值，则可能需要将其传递给代码的其余部分：将该值放在 break 之后，它就会被 loop 表达式返回。let result = loop {break counter * 2;}
21. 在 match 中，若间接地访问一个变量，则不经过重新绑定就无法在分支中再使用它。match 提供了 @ 符号来绑定变量到名称。如n @ 1  ..= 12 =>
22. 另一个好处是：if let 允许匹配枚举非参数化的变量，即枚举未注明 #[derive(PartialEq)]，我们也没有为其实现 PartialEq。在这种情况下，通常 if Foo::Bar==a 会出错，因为此类枚举的实例不具有可比性。但是，if let 是可行的。
23. 点运算符会自动传递&：rectangle.perimeter()` === `Rectangle::perimeter(&rectangle)
24. 调用闭包时， 该变量的变化就意味着闭包内部发生了变化。因此闭包需要是可变的。
25. 当以闭包作为输入参数时，必须指出闭包的完整类型，它是通过使用以下 trait 中的一种来指定的。其受限制程度按以下顺序递减：Fn（&T捕获）、FnMut(&mut T捕获)、FnOnce（T所有权捕获）
26. 在参数里闭包类型是按类似where F: Fn(i32) -> i32这种格式写的，F必须是泛型。闭包从周围的作用域中捕获变量是简单明了的。这样会有某些后果吗？确实有。观察一下使用闭包作为函数参数，这要求闭包是泛型的，闭包定义的方式决定了这是必要的。
27. 既然闭包可以作为参数，你很可能想知道函数是否也可以呢。确实可以！如果你声明一个接受闭包作为参数的函数，那么任何满足该闭包的 trait 约束的函数都可以作为其参数。
28. Rust 只支持返回具体（非泛型）的类型。按照定义，匿名的闭包的类型是未知的，所以只有使用impl Trait才能返回一个闭包。-> impl Fn()
29. 发散函数（diverging function）绝不会返回。 它们使用 ! 标记，这是一个空类型。和所有其他类型相反，这个类型无法实例化，因为此类型可能具有的所有可能值的集合为空。 注意，它与 () 类型不同，后者只有一个可能的值。这种类型的主要优点是它可以被转换为任何其他类型，从而可以在需要精确类型的地方使用，例如在 match 匹配分支。false => continue,因为它永远不会返回，因此不会违反匹配表达式的类型要求。
30. rustc --crate-type=lib rary.rs编译预编译库，rlib后缀，文件名前会加lib（不自定义名称的话）。rustc executable.rs --extern rary=library.rlib使用预编译库。

## 2022/7/10

把根据例子学Rust看完，之后开始看core库和std库文档。

根据例子学Rust部分杂记：

1. 在cargo.toml中添加依赖包后，还要在main.rs中extern
2. cargo.toml清单格式https://doc.rust-lang.org/cargo/reference/manifest.html#the-manifest-format
3. where从句会比正常语法更具有表现力：可以指定泛型是<T>，而用where约束Option<T>: Debug。
4. `<RHS = Self>` 表示 RHS 的默认值为 Self 类型，也就是如果没有在实现中另行指定，RHS 就取 Self 类型。
5. std::marker::PhantomData 可作为虚类型参数的类型，可以充当标记，也可以供编译时类型检查 使用。这些额外的参数没有存储值，也没有运行时行为。
6. Rust 中的析构函数概念是通过 Drop trait 提供的。当资源离开作用域，就调用析构函数。你无需为每种类型都实现 Drop trait，只要为那些需要自己的析构函数逻辑的类型实现就可以了。
7. 在单个变量的解构内，可以同时使用 by-move 和 by-reference 模式绑定。这样做将导致变量的部分移动（partial move），这意味着变量的某些部分将被移动，而其他部分将保留。在这种情况下，后面不能整体使用父级变量，但是仍然可以使用只引用（而不移动）的部分。
8. 不省略的情况下，任何引用都必须拥有标注好的生命周期。任何被返回的引用都必须有和某个输入量相同的生命周期或是静态类型（static）。
9. 方法一般是不需要标明生命周期的，因为 self 的生命周期会赋给所有的输出生命周期参数。
10. T: Trait + 'a：T 类型必须实现 Trait trait，并且在 T 中的所有引用都必须比 'a 活得更长。
11. Clone, 用来从 &T 创建副本 T。比较 trait: Eq, PartialEq, Ord, PartialOrd
12. 相比于直接返回一个 trait 对象，我们的函数返回一个包含一些 Animal 的 Box。box 只是对堆中某些内存的引用。因为引用的大小是静态已知的，并且编译器可以保证引用指向已分配的堆 Animal，所以我们可以从函数中返回 trait！每当在堆上分配内存时，Rust 都会尝试尽可能明确。因此，如果你的函数以这种方式返回指向堆的 trait 指针，则需要使用 dyn 关键字编写返回类型，例如 Box<dyn Animal>。
13. 在 Rust 中，很多运算符可以通过 trait 来重载。也就是说，这些运算符可以根据它们的 输入参数来完成不同的任务。这之所以可行，是因为运算符就是方法调用的语法糖。例 如，a + b 中的 + 运算符会调用 add 方法（也就是 a.add(b)）。这个 add 方 法是 Add trait 的一部分。因此，+ 运算符可以被任何 Add trait 的实现者使用。
14. 如果函数返回实现了 MyTrait 的类型，可以将其返回类型编写为 -> impl MyTrait。这可以大大简化你的类型签名。用于闭包之类的没法写出具体类型的地方。
15. Option 有一个内置方法 map()，这个组合算子可用于 Some -> Some 和 None -> None 这样的简单映射。多个不同的 map() 调用可以串起来，这样更加灵活。
16. and_then() 使用被 Option 包裹的值来调用其输入函数并返回结果。 如果 Option 是 None，那么它返回 None。
17. 处理混合错误类型的最基本的手段就是让它们互相包含。
18. 由于 From::from 是不同类型之间的转换工具，也就是说，如果在错误可转换成返回类型地方使用 ?，它将自动转换成返回类型。
19. Result 实现了 FromIter，因此结果的向量（Vec<Result<T, E>>）可以被转换成 结果包裹着向量（Result<Vec<T>, E>）。一旦找到一个 Result::Err ，遍历就被终止。
20. 在 Rust 中，所有值默认都是栈分配的。
21. 被装箱的值可以使用 * 运算符进行解引用；这会移除掉一层装箱。
22. 任何实现了 Eq 和 Hash trait 的类型都可以充当 HashMap 的键。对自定义类型可以轻松地实现 Eq 和 Hash，只需加上一行代码：#[derive(PartialEq, Eq, Hash)]。
23. HashSet 的独特之处在于，它保证了不会出现重复的元素。这是任何 set 集合类型（set collection）遵循的规定。如果你不想要一样东西出现多于一次，或者你要判断一样东西是不是已经存在，这种做法就很有用了。还能做集合运算。
24. Rust 通过 spawn 函数提供了创建本地操作系统（native OS）线程的机制，该函数的参数是一个通过值捕获变量的闭包（moving closure）。这些线程由操作系统调度（schedule）。
25. .enumerate() 会把当前的迭代计数与被迭代的元素以元组 (index, element)的形式返回。
26. Rust 为线程之间的通信提供了异步的通道（channel）。通道允许两个端点之间信息的 单向流动：Sender（发送端） 和 Receiver（接收端）。被创建的线程取得 `thread_tx` 的所有权，每个线程都把消息放在通道的消息队列中。`recv` 方法从通道中拿到一个消息，若无可用消息的话，`recv` 将阻止当前线程。
27.Rust 注释中的代码块也会被编译并且用作测试。Rust 会隐式地在其中添加`fn main()` 和 `extern crate <cratename>`。
28. 单元测试一次仅能单独测试一个模块，这种测试是小规模的，并且能测试私有 代码；集成测试是 crate 外部的测试，并且仅使用 crate 的公共接口，就像其他使用 该 crate 的程序那样。集成测试的目的是检验你的库的各部分是否能够正确地协同工作。cargo 在与 src 同级别的 tests 目录寻找集成测试。
29. 有时仅在测试中才需要一些依赖（比如基准测试相关的）。这种依赖要写在 Cargo.toml 的 [dev-dependencies] 部分。这些依赖不会传播给其他依赖于这个包的包。
30. 基准测试（benchmark）：对功能进行基准测试，保证其运行速度足够快。

core库部分杂记：

1. memcpy、memcmp、memset - 这些是通常由 LLVM 生成的核心内存例程。当no_std时由rustc编译器内置提供，若rustc没有支持要编译到的目标平台时，可以使用crates.io上的compiler_builtins包https://crates.io/crates/compiler_builtins
2. 声明宏中的重复部分的分隔符，在使用的时候，即使是+也可以不出现。
3. 核心库文档分为三个部分，原生类型、宏和模块，先从这些在crate根的宏看起吧
4. 作为一般规则，只允许在项，语句或表达式位置使用宏。
5. concat_idents可以拼接标识符，暂时看不出来实际作用是什么，估计是用于别的宏中操作标识符用的，加前后缀什么的。简中版标准库好像是翻译错了（?），不能用于定义未出现的标识符翻译成不能用于定义新的宏了。
6. log_syntax输出token树到标准输出。在调试或者编写宏的时候参考学习貌似还挺有用的。
7. 话说，宏不管在解释型语言还是编译型语言，都是指一个可以先于操作数表达式求值之前把操作数整体AST先处理的“函数”，把通常的“先递归求值内部的操作数表达式再求外层表达式”顺序手动的阻断。设计语言的时候貌似要实现这个功能必须硬编码到编译器里（?），这样的话类lisp语言的quote看起来是不可或缺的原语了。
8. 使用不稳定特性都需要加#![feature()]注释
9. trace_macros!(true)每当一个宏被展开时会启用一个编译器信息。在展开后使用trace_macros!(false)来关闭它。
10. assert断言宏，可以在第二个参数以及后续参数使用格式化输出给出断言错误提示内容，assert_eq和assert_ne也是，此外加上debug_前缀则只会在debug模式执行断言

## 2022/7/11

1. cfg! 与 #[cfg] 不同，它不会删除任何代码，只会评估为 true 或 false。 例如，当将 cfg! 用作条件时，无论 cfg! 正在评估什么，if/else 表达式中的所有块都必须有效。
2. column!、line!、file!提供当前位置信息
3. compile_error!拉起一个编译错误
4. concat连接&'static str
5. env!编译器获取环境变量.option_env!如果在编译时存在指定的环境变量，它将扩展为 Option<&'static str> 类型的表达式，其值是环境变量的值的 Some。 如果不存在环境变量，则它将扩展为 None。
6. format_args!作为format!, write!, println!等宏的基础
7. include!文件包含、include_bytes!字节包含、include_str!字符串包含
8. matches!返回给定表达式是否与任何给定模式匹配。
9. module_path扩展为代表当前模块路径的字符串。当前模块路径可以被认为是引回到 crate root 的模块层次结构。 返回路径的第一部分是当前正在编译的 crate 的名称。
10. stringify对其参数进行字符串化。
11. unimplemented! 和 todo! 之间的区别在于，尽管 todo! 传达了稍后实现该功能的意图，并且消息为 “not yet implemented”，但 unimplemented! 并未提出任何此类声明。 它的消息是 “not implemented”。用了这些函数会发生panic。
12. unreachable!不可访问代码，访问即panic。 unreachable! 只是 panic! 的简写，带有固定的、特定的消息。
13. write!和writeln!将格式化的数据写入缓冲区。

接下来看原生类型部分

1. never：永远不会解析为任何值的计算类型。可用于分支中break和continue。实现了Clone、Copy、Debug、Display、Hash、Ord、Eq、PartialOrd、PartialEq。
2. array：实现了map（[T; N]->[U; N]）、zip（[U; N]->[(T, U); N]）、as_slice（相当于&a[..]）、as_mut_slice（相当于&mut a[..]）、each_ref或each_mut（把数组引用里的元素全变成引用，方便在内部元素不是Copy时map发生移动）、split_array_ref或split_array_mut（切分数组引用成两个引用）。如果元素类型允许，则任何大小的数组都将实现以下 traits：Copy、Clone、Debug、IntoIterator、PartialEq、PartialOrd、Eq、Ord、Hash、AsRef、AsMut、Borrow、 BorrowMut
3. bool：bool 实现了各种 traits，例如 BitAnd、BitOr、Not 等，允许我们使用 &、| 和 ! 执行布尔运算。then_some（如果 bool 是 true，则返回 Some(f())，否则返回 None。）、then（如果 bool 是 true，则返回 Some(f())，否则返回 None。）
4. char：一些从utf解码的函数或编码到utf的函数、一些utf编码占用长度的函数、一些转义特殊字符的函数、一些大小写转换函数、一些判断是不是某类字符的函数
5. 整型：提供了一些绝对值abs、平方pow、取对log等函数。一些检查函数、一些溢出处理函数、和bytes的转换函数。
6. 浮点型：正负无穷、NaN等特殊值常量。clamp限制到一定区间、一些检查函数、和bytes的转换函数。
7. tuple：没有非trait实现，全都是trait
8. fn：函数指针，普通函数指针是通过强制转换不能捕获环境的普通函数或闭包而获得的。普通 fn() 函数指针只能指向安全函数，而 unsafe fn() 函数指针可以指向安全或不安全函数。指向带有 C ABI 的函数的指针的类型为extern "C" fn()。

## 2022/7/12

继续看了原生类型部分：

1. pointer：裸指针。提供一些指针计算和指针转换函数。
2. reference：引用。&mut T 引用可以自由强制转换为 &T 引用类型相同的引用，生命周期较长的引用可以自由强制转换为较短的引用。
3. slice：切片是一个内存块的视图，表示为一个指针和一个长度。
4. str：实现了一些字符串切片的转换、查找、拆分、判断函数。
5. unit：()

## 2022/7/13

开始看core的模块：
1. assert_matches：有俩宏，看起来和assert宏也没什么区别。
2. async_iter（streams）：异步迭代器。模块分为traits、function、structs三部分。
	* trait：里面就一个Asynclterator的trait，需要手动实现poll_next()。
		1) poll_next()返回Poll::Pending未准备好、Poll::Ready(Some(val))准备好了、Poll::Ready(None)已经空了三种枚举类型。
		2) 提供size_hint()返回剩余迭代器上下界数组(usize, Option<usize>)
	* fn：提供了一个把普通迭代器转换为异步迭代器的函数from_iter()
	* structs：由from_iter()产生的类型
3. intrinsics：内置函数，这个模块里定义的都是函数
4. lazy：惰性求值，内含两个结构体，Lazy是个首次访问时才会初始化的值，OnceCell是一次写入的Cell
5. panicking：对core的panic支持。
6. simd：便携式SIMD模块。
7. unicode：unicode支持。
8. alloc：内存分配相关。分为traits、structs、type definitions。可以自行实现内存分配器，然后用#[global_allocator]标记它。
9. any：dyn Any用于运行时反射，测试某个值会是什么类型。大多数类型都实现了 Any。但是，任何包含非 `static’ 引用的类型都不会。
10. arch：包含两个用来使用内联汇编的宏asm和global_asm。以及各个架构对应的模块，里面包含架构的内部函数。
11. array：数组的辅助函数和结构体。
12. ascii：主要就一个函数escape_default用于ascii转义
13. borrow：不可变借用和可变借用trait。实现borrow()可借用。
14. cell：可共享可变封装，提供了几种不同的Cell结构体。依托于UnsafeCell。
15. char：char的一些辅助函数。
16. clone：显式复制。
17. cmp：排序和比较。Eq 和 PartialEq 是 traits，允许您分别定义值之间的完全相等和部分相等。 实现它们会使 == 和 != 运算符重载。Ord 和 PartialOrd 是 traits，允许您分别定义值之间的全部排序和部分排序。
18. convert：类型转换。AsMut、AsRef、From、Into。
19. default：提供Default的trait。
20. ffi：提供了c指针和可变参数ABI实现
21. fmt：格式化字符串。提供了Binary、Debug、Display、LowerExp、LowerHex、Octal、Pointer、UpperExp、UpperHex各种格式的。以及写入缓冲区的write。
22. future：异步模块。future 是一个可能尚未完成计算的值。 这种异步值使得，线程在等待值变为可用时，可以继续执行有用的工作。future 的核心方法 poll 试图将 future 解析为最终值。 如果值未准备好，则此方法不会阻塞。 取而代之的是，如果有可能通过再次轮询来取得进一步的进展，则计划将当前任务唤醒。
23. hash：哈希计算支持。
24. hint：三个hint相关函数。
25. iter：各种类型的迭代器。
26. marker：Copy、Send、Sized、Sync、Unpin的trait。
27. mem：一些函数，用于处理内存。ManuallyDrop包装器，用于禁止编译器自动调用 T 的析构函数。
	1) forget关闭析构。forget 没有标记为 unsafe，因为 Rust 的安全保证不包括析构函数将始终运行的保证。从安全代码允许 mem::forget 不会从根本上改变 Rust 的安全保证。
	2) drop显式析构
	3) align_of获取对齐。align_of返回 ABI 要求的类型的最小对齐方式、align_of_val返回 ABI 所需的 val 指向的值的类型的最小对齐方式。
	4) size_of获取类型大小。size_of返回类型的大小 (以字节为单位)，size_of_val返回所指向的值的大小 (以字节为单位)。
	5) discriminant返回一个唯一标识 v 中的枚举变体的值。
	6) needs_drop如果需要丢弃则返回true
	7) replace将 src 移至引用的 dest，返回先前的 dest 值。
	8) swap在两个可变位置交换值，而无需对其中一个进行初始化。
	9) take用默认值 T 替换 dest，并返回以前的 dest 值。
	10) transmute将一种类型的值的位重新解释为另一种类型。两种类型都必须具有相同的大小。
	11) transmute_copy将 src 解释为具有 &U 类型，然后在不移动所包含的值的情况下读取 src。
	12) zeroed返回由全零字节模式表示的 T 类型的值。
28. num：内置数字类型，非零数之类的。
29. ops：各种运算符重载。
30. option：可选值。
	1) 如果 Option 分别为 Some 或 None，则 is_some 和 is_none 方法返回 true。
	2) and_then 和 or_else 方法将函数作为输入，并且仅在需要产生新值时才评估函数。只有 and_then 方法可以生成具有与 Option<T> 不同的内部类型 U 的 Option<U> 值。
31. panic：panic支持。一些栈回溯的内容。
32. pin：结构体Pin<P>这是一种指针的包装，该指针使该指针 “pin” 成为其值，从而防止该指针引用的值被移动，除非它实现 Unpin。
33. ptr：裸指针。
34. result：和option类似。
35. slice：切片管理和操作。
36. str：字符串相关类型。
37. sync：内部有个atomic模块，关于锁与内存共享。
38. task：异步处理相关结构。
39. time：包含一个时间跨度的结构体Duration。

std库 比core多了backtrace、boxed、collections、env、error、fs、io、net、os、path、process、rc、vec

## 2022/7/14

今天试着把用Nim写的毕设Trep移植到Rust上来，作为Rust学习的最后一个练手。今天完成了大部分的lexer，工作量还是挺大的，要尽快弄完然后开始os-lab了。

今天发现的一些问题：

1. 如果变量名太长，可能是模块层次化做的不到位，要再细分层次和模块。
2. 父模块中的名称也不是直接用的，还是要用super路径或者use super。

## 2022/7/15

继续迁移毕设到rust。然后开始做os-lib。

看了第零章的指导：

1. 操作系统远古阶段的 LibOS设计思路在当前云计算时代重新焕发青春，成为学术机构和各大互联网企业探索的新热点。
2. 逐步进化的“小”操作系统：
	* LibOS: 让APP与HW隔离，简化应用访问硬件的难度和复杂性
	* BatchOS： 让APP与OS隔离，加强系统安全，提高执行效率
	* multiprog&time-sharing OS: 让APP共享CPU资源
	* Address Space OS: 隔离APP访问的内存地址空间，加强APP间的安全
	* Process OS: 支持APP动态创建新进程，增强进程管理和资源管理能力
	* Filesystem OS：支持APP对数据的持久保存
	* IPC OS：支持多个APP进程间数据交互与事件通知
	* Tread&Coroutine OS：支持线程和协程APP，简化切换与数据共享
	* SyncMutex OS：在多线程APP中支持对共享资源的同步互斥访问
	* Device OS：提高APP的I/O效率和人机交互能力，支持基于外设中断的串口/块设备/键盘/鼠标/显示设备
3. 一个操作系统（OS）是一个软件，它帮助用户和应用程序使用和管理计算机的资源。我们的讨论将集中在通用操作系统上，因为它们需要的技术是嵌入式系统所需技术的超集，对操作系统原理、概念和技术的覆盖更加全面。
4. 操作系统不能只提供面向单一编程语言的函数库的编程接口 (API, Application Programming Interface) ，它的接口需要考虑对基于各种编程语言的应用支持，以及访问安全等因素，使得应用软件不能像访问函数库一样的直接访问操作系统内部函数，更不能直接读写操作系统内部的地址空间。为此，操作系统设计了一套安全可靠的接口，我们称为系统调用接口 (System Call Interface)。系统调用接口通常面向应用程序提供了API的描述，但在具体实现上，还需要提供ABI的接口描述规范。在现代处理器的安全支持（特权级隔离，内存空间隔离等）下，应用程序就不能直接以函数调用的方式访问操作系统的函数，以及直接读写操作系统的数据变量。不同类型的应用程序可以通过符合操作系统规定的ABI规范的系统调用接口，发出系统调用请求，来获得操作系统的服务。操作系统提供完服务后，返回应用程序继续执行。
5. 相对比较重要的操作系统接口或抽象：
	* 进程（即程序运行过程）管理：复制创建进程 fork 、退出进程 exit 、执行进程 exec 等。
	* 线程管理：线程（即程序的一个执行流）的创建、执行、调度切换等。
	* 线程同步互斥的并发控制：互斥锁 mutex 、信号量 semaphore 、管程 monitor 、条件变量 condition variable 等。
	* 进程间通信：管道 pipe 、信号 signal 、事件 event 等。
	* 虚存管理：内存空间映射 mmap 、改变数据段地址空间大小 sbrk 、共享内存 shm 等。
	* 文件I/O操作：对存储设备中的文件进行读 read 、写 write 、打开 open 、关闭 close 等操作。
	* 外设I/O操作：外设包括键盘、显示器、串口、磁盘、时钟 … 但接口均采用了文件 I/O 操作的通用系统调用接口。
6. 操作系统对计算机硬件重要组成的抽象和虚拟化，使得应用程序只需基于对简单的抽象概念的访问来到达对计算机系统资源的使用：
	* 文件 (File) 是外设的一种抽象和虚拟化。特别对于存储外设而言，文件是持久存储的抽象。
	* 地址空间 (Address Space) 是对内存的抽象和虚拟化。
	* 进程 (Process) 是对计算机资源的抽象和虚拟化。而其中最核心的部分是对CPU的抽象与虚拟化。
7. 对cpu来说，有普通控制流和异常控制流之分。这里我们把控制流在执行完某指令时的物理资源内容，即确保下一时刻能继续 正确 执行控制流指令的物理资源内容称为控制流的 上下文(Context) ，也可称为控制流所在执行环境的状态。操作系统有责任来保护应用程序中控制流的上下文，以让应用程序得以正确执行。
8. 在操作系统中，需要处理三类异常控制流：外设中断 (Device Interrupt) 、陷入 (Trap) 和异常 (Exception，也称Fault Interrupt)。中断 (Interrupt) 由外部设备引起的外部 I/O 事件如时钟中断、控制台中断等。异常 (Exception) 是在处理器执行指令期间检测到不正常的或非法的内部事件（如除零错、地址访问越界）。陷入 (Trap) 是在程序中使用请求操作系统服务的系统调用而引发的有意事件。
9. 在 RISC-V 的特权级规范文档中，异常指的是由于 CPU 当前指令执行而产生的异常控制流，中断指的是与 CPU 当前指令执行无关的异常控制流，中断和异常统称为陷入。
10. 地址空间 (Address Space) 是对物理内存的虚拟化和抽象，也称虚存 (Virtual Memory)。它就是操作系统通过处理器中的内存管理单元 (MMU, Memory Management Unit) 硬件的支持而给应用程序和用户提供一个大的（可能超过计算机中的物理内存容量）、连续的（连续的地址空间编址）、私有的（其他应用程序无法破坏）的存储空间。操作系统中的虚存管理与处理器的 MMU 密切相关,在启动虚存机制后，软件通过 CPU 访问的每个虚拟地址都需要通过 CPU 中的 MMU 转换为一个物理地址来进行访问。
11. 以磁盘为代表的持久存储介质的数据访问单位是一个扇区或一个块，而在内存中的数据访问单位是一个字节或一个字。这就需要操作系统通过文件来屏蔽磁盘与内存差异，尽量以内存的读写方式来处理持久存储的数据。
12. 各种外设虽然差异很大，但也有基本的读写操作，可以通过文件来进行统一的抽象，并在操作系统内部实现中来隐藏对外设的具体访问过程，从而让用户可以以统一的文件操作来访问各种外设。
13. 操作系统具有五个方面的特征：虚拟化 (Virtualization)、并发性 (Concurrency)、异步性、共享性和持久性 (Persistency)。
14. 并行和并发：并行 (Parallel) 是指两个或者多个事件在同一时刻发生；并发 (Concurrent) 是指两个或多个事件在同一时间间隔内发生。

## 2022/7/16

迁移毕设，看test1的指导：

1. 本章展现了操作系统的一个基本目标LibOS：让应用与硬件隔离，简化了应用访问硬件的难度和复杂性。
2. 无论用户态应用如何编写，是手写汇编代码，还是基于某种高级编程语言调用其标准库或三方库，某些功能总要直接或间接的通过内核/操作系统提供的 系统调用 (System Call) 来实现。内核作为用户态的执行环境，它不仅要提供系统调用接口，还需要对用户态应用的执行进行监控和管理。
3. rustc --print target-list | grep riscv查看rustc支持的riscv目标三元组
4. 从 CPU 的视角看来，可以将物理内存看成一个大字节数组，而物理地址则对应于一个能够用来访问数组中某个元素的下标。与我们日常编程习惯不同的是，该下标通常不以 0 开头，而通常以一个常数，如 0x80000000 开头。简言之，CPU 可以通过物理地址来寻址，并 逐字节 地访问物理内存中保存的数据。
5. 一个多位数的低位放在较小的地址处，高位放在较大的地址处，则称小端序（little-endian）；反之则称大端序（big-endian）。常见的 x86、RISC-V 等架构采用的是小端序。
6. 不同的段会被编译器放置在内存不同的位置上，这构成了程序的 内存布局 (Memory Layout)。
7. 代码部分只有代码段 .text 一个段，存放程序的所有汇编代码。而数据部分则还可以继续细化：
	* 已初始化数据段保存程序中那些已初始化的全局数据，分为 .rodata 和 .data 两部分。前者存放只读的全局数据，通常是一些常数或者是 常量字符串等；而后者存放可修改的全局数据。
	* 未初始化数据段 .bss 保存程序中那些未初始化的全局数据，通常由程序的加载者代为进行零初始化，即将这块区域逐字节清零；
堆 （heap）区域用来存放程序运行时动态分配的数据，如 C/C++ 中的 malloc/new 分配到的数据本体就放在堆区域，它向高地址增长；
栈 （stack）区域不仅用作函数调用上下文的保存与恢复，每个函数作用域内的局部变量也被编译器放在它的栈帧内，它向低地址增长。
8. 汇编器输出的每个目标文件都有一个独立的程序内存布局，它描述了目标文件内各段所在的位置。而链接器所做的事情是将所有输入的目标文件整合成一个整体的内存布局。
9. rust编写内核态程序步骤：  
	关闭标准库#![no_std]->添加panic语言项绑定#[panic_handler]->关闭main函数#![no_main]->ld指定入口点->汇编在入口点分配并启用栈，跳转至rust入口->ld调整内存布局->rust-objcopy --strip-all修剪掉元数据
10. 利用 rust-objcopy 工具可以删除掉 ELF 文件中的 所有 header 只保留各个段的实际数据得到一个没有任何符号的纯二进制镜像文件
11. 其他控制流都只需要跳转到一个 编译期固定下来 的地址，而函数调用的返回跳转是跳转到一个 运行时确定 （确切地说是在函数调用发生的时候）的地址。
12. 调用规范 (Calling Convention) 约定在某个指令集架构上，某种编程语言的函数调用如何实现。它包括了以下内容：函数的输入参数和返回值如何传递；函数调用上下文中调用者/被调用者保存寄存器的划分；其他的在函数调用流程中对于寄存器的使用方法。
13. 感觉各种高低级语言之间的交互主要还是靠符号来实现，这样的话#[no_mangle]是经常要用到的
14. 使用内联汇编ecall来调用sbi

## 2022/7/17

看了test2的指导：

1. 本章构造一个包含操作系统内核和多个应用程序的单一执行程序BatchOS：多程序自动按顺序加载，使用特权级。
2. 处理器设置两个不同安全等级的执行环境：用户态特权级的执行环境和内核态特权级的执行环境。且明确指出可能破坏计算机系统的内核态特权级指令子集，规定内核态特权级指令子集中的指令只能在内核态特权级的执行环境中执行。处理器在执行指令前会进行特权级安全检查，如果在用户态执行环境中执行这些内核态特权级指令，会产生异常。
3. M 模式软件 SEE 和 S 模式的内核之间的接口被称为 监督模式二进制接口(Supervisor Binary Interface, SBI)，而内核和 U 模式的应用程序之间的接口被称为 应用程序二进制接口 (Application Binary Interface, ABI)，当然它有一个更加通俗的名字—— 系统调用 (syscall, System Call) 。
4. 与特权级无关的一般的指令和通用寄存器 x0~ x31 在任何特权级都可以执行。而每个特权级都对应一些特殊指令和 控制状态寄存器(CSR, Control and Status Register) ，来控制该特权级的某些行为并描述其状态。当然特权指令不仅具有读写 CSR 的指令，还有其他功能的特权指令。
5. 在 RISC-V 中，会有两类属于高特权级 S 模式的特权指令：
	* 指令本身属于高特权级的指令，如 sret 指令（表示从 S 模式返回到 U 模式）。
	* 指令访问了 S模式特权级下才能访问的寄存器 或内存，如表示S模式系统状态的 控制状态寄存器 sstatus 等。
6. 应用放置采用“静态绑定”的方式，而操作系统加载应用则采用“动态加载”的方式
7. 在执行操作系统的 Trap 处理过程（会修改通用寄存器）之前，我们需要在某个地方（某内存块或内核的栈）保存这些寄存器并在 Trap 处理结束后恢复这些寄存器。
8. 当 CPU 执行完一条指令（如 ecall ）并准备从用户特权级 陷入（ Trap ）到 S 特权级的时候，硬件会自动完成如下这些事情：  
	* sstatus 的 SPP 字段会被修改为 CPU 当前的特权级（U/S）。
	* sepc 会被修改为 Trap 处理完成后默认会执行的下一条指令的地址。
	* scause/stval 分别会被修改成这次 Trap 的原因以及相关的附加信息。
	* CPU 会跳转到 stvec 所设置的 Trap 处理入口地址，并将当前特权级设置为 S ，然后从Trap 处理入口地址处开始执行。
9. 在 RV64 中， stvec 是一个 64 位的 CSR，在中断使能的情况下，保存了中断处理的入口地址。它有两个字段：
	* MODE 位于 [1:0]，长度为 2 bits；
	* BASE 位于 [63:2]，长度为 62 bits。
	* 当 MODE 字段为 0 的时候， stvec 被设置为 Direct 模式，此时进入 S 模式的 Trap 无论原因如何，处理 Trap 的入口地址都是 BASE<<2 ， CPU 会跳转到这个地方进行异常处理。本书中我们只会将 stvec 设置为 Direct 模式。而 stvec 还可以被设置为 Vectored 模式。
10. 而当 CPU 完成 Trap 处理准备返回的时候，需要通过一条 S 特权级的特权指令 sret 来完成，这一条指令具体完成以下功能：
	* CPU 会将当前的特权级按照 sstatus 的 SPP 字段设置为 U 或者 S ；
	* CPU 会跳转到 sepc 寄存器指向的那条指令，然后继续执行。
11. 在正式进入 S 特权级的 Trap 处理之前，上面 提到过我们必须保存原控制流的寄存器状态，这一般通过内核栈来保存。
12. 特权级切换的核心是对Trap的管理。这主要涉及到如下一些内容：
	* 应用程序通过 ecall 进入到内核状态时，操作系统保存被打断的应用程序的 Trap 上下文；
	* 操作系统根据Trap相关的CSR寄存器内容，完成系统调用服务的分发与处理；
	* 操作系统完成系统调用服务后，需要恢复被打断的应用程序的Trap 上下文，并通 sret让应用程序继续执行。
	
## 2022/7/18

看了test3的指导，开始分析代码，做实验：

1. 本章是multiprog&time-sharing OS: 让APP共享CPU资源
2. 我们可以把一个程序的一次完整执行过程称为一次 任务 (Task)，把一个程序在一个时间片（Time Slice）上占用处理器执行的过程称为一个 任务片 (Task Slice)。
3. 多个应用程序被一次性地加载到内存中，这样在切换到另外一个应用程序执行会很快，不像前一章介绍的操作系统，还要有清空前一个应用，然后加载当前应用的过程开销。
4. 如何知道外设是否已经完成了请求呢？通常外设会提供一个可读的寄存器记录它目前的工作状态，于是 CPU 需要不断原地循环读取它直到它的结果显示设备已经将请求处理完毕了，才能继续执行（这就是 忙等 的含义）。
5. 多道程序的思想在于：内核同时管理多个应用。如果外设处理 I/O 的时间足够长，那我们可以先进行任务切换去执行其他应用；在某次切换回来之后，应用再次读取设备寄存器，发现 I/O 请求已经处理完毕了，那么就可以根据返回的 I/O 结果继续向下执行了。
6. 协作式调度(Cooperative Scheduling) ，因为它的特征是：只要一个应用不主动 yield 交出 CPU 使用权，它就会一直执行下去。与之相对， 抢占式调度(Preemptive Scheduling) 则是应用 随时 都有被内核切换出去的可能。可以从性能（主要是吞吐量和延迟两个指标）和 公平性(Fairness) 两个维度来评价调度算法，后者要求多个应用分到的时间片占比不应差距过大。
7. 对于某个处理器核而言， 异常与当前 CPU 的指令执行是 同步 (Synchronous) 的，异常被触发的原因一定能够追溯到某条指令的执行；而中断则 异步 (Asynchronous) 于当前正在进行的指令，也就是说中断来自于哪个外设以及中断如何触发完全与处理器正在执行的当前指令无关。而对于中断，可以理解为发起中断的是一套与处理器执行指令无关的电路（从时钟中断来看就是简单的计数和比较器），这套电路仅通过一根导线接入处理器。当外设想要触发中断的时候则输入一个高电平或正边沿，处理器会在每执行完一条指令之后检查一下这根线，看情况决定是继续执行接下来的指令还是进入中断处理流程。也就是说，大多数情况下，指令执行的相关硬件单元和可能发起中断的电路是完全独立 并行 (Parallel) 运行的，它们中间只有一根导线相连。
8. RISC-V 的中断可以分成三类：
	* 软件中断 (Software Interrupt)：由软件控制发出的中断
	* 时钟中断 (Timer Interrupt)：由时钟电路发出的中断
	* 外部中断 (External Interrupt)：由外设发出的中断
9. 中断每一个都有 M/S 特权级两个版本。中断的特权级可以决定该中断是否会被屏蔽，以及需要 Trap 到 CPU 的哪个特权级进行处理。在判断中断是否会被屏蔽的时候，有以下规则：
	* 如果中断的特权级低于 CPU 当前的特权级，则该中断会被屏蔽，不会被处理；
	* 如果中断的特权级高于与 CPU 当前的特权级或相同，则需要通过相应的 CSR 判断该中断是否会被屏蔽。
10. 如果中断没有被屏蔽，那么接下来就需要软件进行处理，而具体到哪个特权级进行处理与一些中断代理 CSR 的设置有关。默认情况下，所有的中断都需要到 M 特权级处理。而通过软件设置这些中断代理 CSR 之后，就可以到低特权级处理，但是 Trap 到的特权级不能低于中断的特权级。事实上所有的中断/异常默认也都是到 M 特权级处理的。
11. 嵌套 Trap 则是指处理一个 Trap（可能是中断或异常）的过程中又再次发生 Trap ，嵌套中断是嵌套 Trap 的一个特例。在内核开发时我们需要仔细权衡哪些嵌套 Trap 应当被允许存在，哪些嵌套 Trap 又应该被禁止，这会关系到内核的执行模型。
12. 由于软件（特别是操作系统）需要一种计时机制，RISC-V 架构要求处理器要有一个内置时钟，其频率一般低于 CPU 主频。此外，还有一个计数器用来统计处理器自上电以来经过了多少个内置时钟的时钟周期。在 RISC-V 64 架构上，该计数器保存在一个 64 位的 CSR mtime中，我们无需担心它的溢出问题，在内核运行全程可以认为它是一直递增的。
13. 目前为了简单起见，我们的内核不会被 S 特权级中断所打断，这是因为 CPU 在 S 特权级时， sstatus.sie总为 0 。但这会造成内核对部分中断的响应不及时，因此一种较为合理的做法是允许内核在处理系统调用的时候被打断优先处理某些中断，这是一种允许 Trap 嵌套的设计。

## 2022/7/19

做完了test3的实验

## 2022/7/20

看test4的教程:

1. 本章Address Space OS: 隔离APP访问的内存地址空间，加强APP间的安全
2. 实现地址空间的第一步就是实现分页机制，建立好虚拟内存和物理内存的页映射关系。此过程需要硬件支持，硬件细节与具体CPU相关，涉及地址映射机制等，相对比较复杂。
3. 操作系统如果要建立页表（构建虚实地址映射关系），首先要能管理整个系统的物理内存，这就需要知道整个计算机系统的物理内存空间的范围，物理内存中哪些区域是空闲可用的，哪些区域放置内核/应用的代码和数据。
4. 操作系统内核能够以物理页帧为单位分配和回收物理内存，具体实现主要集中在 os/src/mm/frame_allocator.rs 中；也能在虚拟内存中以各种粒度大小来动态分配内存资源，具体实现主要集中在 os/src/mm/heap_allocator.rs 中。
5. 一旦使能分页机制，CPU 访问到的地址都是虚拟地址了，那么内核中也将基于虚地址进行虚存访问。所以在给应用添加虚拟地址空间前，内核自己也会建立一个页表，把整块物理内存通过简单的恒等映射（即虚拟地址映射到对等的物理地址）映射到内核虚拟地址空间中。
6. 在内核地址空间中执行的内核代码常常需要读写应用的地址空间中的数据，这无法简单的通过一次访存来解决，而是需要手动查用户态应用的地址空间的页表，知道用户态应用的虚地址对应的物理地址后，转换成对应的内核态的虚地址，才能访问应用地址空间中的数据。
7. alloc 库需要我们提供给它一个 全局的动态内存分配器 ，它会利用该分配器来管理堆空间，从而使得与堆相关的智能指针或容器数据结构可以正常工作。
8. 只需将我们的动态内存分配器类型实例化为一个全局变量，并使用 #[global_allocator] 语义项标记即可。
9. 如果在地址转换过程中，无法找到物理地址或访问权限有误，则处理器产生非法访问内存的异常错误。
10. 在每个应用程序的视角里，操作系统分配给应用程序一个地址范围受限（容量很大），独占的连续地址空间（其中有些地方被操作系统限制不能访问，如内核本身占用的虚地址空间等）
11. 操作系统要达到地址空间抽象的设计目标，需要有计算机硬件的支持，这就是计算机组成原理课上讲到的 MMU 和 TLB 等硬件机制。
12. 需要硬件提供一些寄存器，软件可以对它进行设置来控制 MMU 按照哪个应用的地址映射关系进行地址转换。
13. 内核以页为单位进行物理内存管理。每个应用的地址空间可以被分成若干个（虚拟） 页面 (Page) ，而可用的物理内存也同样可以被分成若干个（物理） 页帧 (Frame) ，虚拟页面和物理页帧的大小相同。
14. 为了方便实现虚拟页面到物理页帧的地址转换，我们给每个虚拟页面和物理页帧一个编号，分别称为 虚拟页号 (VPN, Virtual Page Number) 和 物理页号 (PPN, Physical Page Number) 。每个应用都有一个表示地址映射关系的 页表 (Page Table) ，里面记录了该应用地址空间中的每个虚拟页面映射到物理内存中的哪个物理页帧，即数据实际被内核放在哪里。
15. 当 MMU 进行地址转换的时候，虚拟地址会分为两部分（虚拟页号，页内偏移），MMU首先找到虚拟地址所在虚拟页面的页号，然后查当前应用的页表，根据虚拟页号找到物理页号；最后按照虚拟地址的页内偏移，给物理页号对应的物理页帧的起始地址加上一个偏移量，这就得到了实际访问的物理地址。
16. 在页表中，还针对虚拟页号设置了一组保护位，它限制了应用对转换得到的物理地址对应的内存的使用方式。最典型的如 rwx ， r 表示当前应用可以读该内存； w 表示当前应用可以写该内存； x 则表示当前应用可以从该内存取指令用来执行。一旦违反了这种限制则会触发异常，并被内核捕获到。
17. 默认情况下 MMU 未被使能，此时无论 CPU 位于哪个特权级，访存的地址都会作为一个物理地址交给对应的内存控制单元来直接访问物理内存。我们可以通过修改 S 特权级的一个名为 satp 的 CSR 来启用分页模式，在这之后 S 和 U 特权级的访存地址会被视为一个虚拟地址，它需要经过 MMU 的地址转换变为一个物理地址，再通过它来访问物理内存；而 M 特权级的访存地址，我们可设定是内存的物理地址。
18. 我们采用分页管理，单个页面的大小设置为 4KiB ，每个虚拟页面和物理页帧都对齐到这个页面大小，也就是说虚拟/物理地址区间 [0,4KiB) 为第 0 个虚拟页面/物理页帧，而 [4KiB,8KiB) 为第 1 个，以此类推。
19. 4KiB需要用 12 位字节地址来表示，因此虚拟地址和物理地址都被分成两部分：它们的低 12 位，即 [11:0] 被称为 页内偏移 (Page Offset) ，它描述一个地址指向的字节在它所在页面中的相对位置。而虚拟地址的高 27 位，即 [38:12] 为它的虚拟页号 VPN，同理物理地址的高 44 位，即 [55:12] 为它的物理页号 PPN，页号可以用来定位一个虚拟/物理地址属于哪一个虚拟页面/物理页帧。
20. 地址转换是以页为单位进行的，在地址转换的前后地址的页内偏移部分不变。可以认为 MMU 只是从虚拟地址中取出 27 位虚拟页号，在页表中查到其对应的物理页号（如果存在的话），最后将得到的44位的物理页号与虚拟地址的12位页内偏移依序拼接到一起就变成了56位的物理地址。
21. SV39 分页模式规定 64 位虚拟地址的 [63:39] 这 25 位必须和第 38 位相同，否则 MMU 会直接认定它是一个不合法的虚拟地址。通过这个检查之后 MMU 再取出低 39 位尝试将其转化为一个 56 位的物理地址。
22. 也就是说，所有 264 个虚拟地址中，只有最低的 256GiB （当第 38 位为 0 时）以及最高的 256GiB（当第 38 位为 1 时）是可能通过 MMU 检查的。当我们写软件代码的时候，一个地址的位宽毋庸置疑就是 64 位，我们要清楚可用的只有最高和最低这两部分
23. 有更多的标志位，物理页号和全部的标志位以某种固定的格式保存在一个结构体中，它被称为 页表项 (PTE, Page Table Entry) ，是利用虚拟页号在页表中查到的结果。
24. 页表项，其中 [53:10] 这 44 位是物理页号，最低的 8 位 [7:0] 则是标志位，它们的含义如下（请注意，为方便说明，下文我们用 页表项的对应虚拟页面 来表示索引到一个页表项的虚拟页号对应的虚拟页面）：
* V(Valid)：仅当位 V 为 1 时，页表项才是合法的；
* R(Read)/W(Write)/X(eXecute)：分别控制索引到这个页表项的对应虚拟页面是否允许读/写/执行；
* U(User)：控制索引到这个页表项的对应虚拟页面是否在 CPU 处于 U 特权级的情况下是否被允许访问；
* G：暂且不理会；
* A(Accessed)：处理器记录自从页表项上的这一位被清零之后，页表项的对应虚拟页面是否被访问过；
* D(Dirty)：处理器记录自从页表项上的这一位被清零之后，页表项的对应虚拟页面是否被修改过。
25. 除了 G 外的上述位可以被操作系统设置，只有 A 位和 D 位会被处理器动态地直接设置为 1，表示对应的页被访问过或修过
26. 27 位的虚拟页号可以看成一个长度 n=3 的字符串，字符集为 α={0,1,2,...,511} ，因为每一位字符都由 9 个比特组成。而我们也不再维护所谓字符串的计数，而是要找到字符串（虚拟页号）对应的页表项。因此，每个叶节点都需要保存 512 个 8 字节的页表项，一共正好 4KiB ，可以直接放在一个物理页帧内。这种页表实现被称为 多级页表 (Multi-Level Page-Table) 。由于 SV39 中虚拟页号被分为三级 页索引 (Page Index) ，因此这是一种三级页表。
27. 非叶节点（页目录表，非末级页表）的表项标志位含义和叶节点（页表，末级页表）相比有一些不同：
* 当 V 为 0 的时候，代表当前指针是一个空指针，无法走向下一级节点，即该页表项对应的虚拟地址范围是无效的；
* 只有当 V 为1 且 R/W/X 均为 0 时，表示是一个合法的页目录表项，其包含的指针会指向下一级的页表；
* 注意: 当 V 为1 且 R/W/X 不全为 0 时，表示是一个合法的页表项，其包含了虚地址对应的物理页号。

## 2022/7/21

做test4

## 2022/7/22

继续做test4，今天终于理顺了实验已有的代码，明天开始写作业

## 2022/7/23

把test4实验部分代码完成了，明天debug

## 2022/7/24

花了一整天终于把bug处理完了，明天开下一个lab

## 2022/7/25

看了test5的教程：

1. 本章Address Space OS: 隔离APP访问的内存地址空间，加强APP间的安全
2. 实现地址空间的第一步就是实现分页机制，建立好虚拟内存和物理内存的页映射关系。此过程需要硬件支持，硬件细节与具体CPU相关，涉及地址映射机制等，相对比较复杂。
3. 操作系统如果要建立页表（构建虚实地址映射关系），首先要能管理整个系统的物理内存，这就需要知道整个计算机系统的物理内存空间的范围，物理内存中哪些区域是空闲可用的，哪些区域放置内核/应用的代码和数据。
4. 操作系统内核能够以物理页帧为单位分配和回收物理内存，具体实现主要集中在 os/src/mm/frame_allocator.rs 中；也能在虚拟内存中以各种粒度大小来动态分配内存资源，具体实现主要集中在 os/src/mm/heap_allocator.rs 中。
5. 一旦使能分页机制，CPU 访问到的地址都是虚拟地址了，那么内核中也将基于虚地址进行虚存访问。所以在给应用添加虚拟地址空间前，内核自己也会建立一个页表，把整块物理内存通过简单的恒等映射（即虚拟地址映射到对等的物理地址）映射到内核虚拟地址空间中。
6. 在内核地址空间中执行的内核代码常常需要读写应用的地址空间中的数据，这无法简单的通过一次访存来解决，而是需要手动查用户态应用的地址空间的页表，知道用户态应用的虚地址对应的物理地址后，转换成对应的内核态的虚地址，才能访问应用地址空间中的数据。
7. alloc 库需要我们提供给它一个 全局的动态内存分配器 ，它会利用该分配器来管理堆空间，从而使得与堆相关的智能指针或容器数据结构可以正常工作。
8. 只需将我们的动态内存分配器类型实例化为一个全局变量，并使用 #[global_allocator] 语义项标记即可。
9. 如果在地址转换过程中，无法找到物理地址或访问权限有误，则处理器产生非法访问内存的异常错误。
10. 在每个应用程序的视角里，操作系统分配给应用程序一个地址范围受限（容量很大），独占的连续地址空间（其中有些地方被操作系统限制不能访问，如内核本身占用的虚地址空间等）
11. 操作系统要达到地址空间抽象的设计目标，需要有计算机硬件的支持，这就是计算机组成原理课上讲到的 MMU 和 TLB 等硬件机制。
12. 需要硬件提供一些寄存器，软件可以对它进行设置来控制 MMU 按照哪个应用的地址映射关系进行地址转换。
13. 内核以页为单位进行物理内存管理。每个应用的地址空间可以被分成若干个（虚拟） 页面 (Page) ，而可用的物理内存也同样可以被分成若干个（物理） 页帧 (Frame) ，虚拟页面和物理页帧的大小相同。
14. 为了方便实现虚拟页面到物理页帧的地址转换，我们给每个虚拟页面和物理页帧一个编号，分别称为 虚拟页号 (VPN, Virtual Page Number) 和 物理页号 (PPN, Physical Page Number) 。每个应用都有一个表示地址映射关系的 页表 (Page Table) ，里面记录了该应用地址空间中的每个虚拟页面映射到物理内存中的哪个物理页帧，即数据实际被内核放在哪里。
15. 当 MMU 进行地址转换的时候，虚拟地址会分为两部分（虚拟页号，页内偏移），MMU首先找到虚拟地址所在虚拟页面的页号，然后查当前应用的页表，根据虚拟页号找到物理页号；最后按照虚拟地址的页内偏移，给物理页号对应的物理页帧的起始地址加上一个偏移量，这就得到了实际访问的物理地址。
16. 在页表中，还针对虚拟页号设置了一组保护位，它限制了应用对转换得到的物理地址对应的内存的使用方式。最典型的如 rwx ， r 表示当前应用可以读该内存； w 表示当前应用可以写该内存； x 则表示当前应用可以从该内存取指令用来执行。一旦违反了这种限制则会触发异常，并被内核捕获到。
17. 默认情况下 MMU 未被使能，此时无论 CPU 位于哪个特权级，访存的地址都会作为一个物理地址交给对应的内存控制单元来直接访问物理内存。我们可以通过修改 S 特权级的一个名为 satp 的 CSR 来启用分页模式，在这之后 S 和 U 特权级的访存地址会被视为一个虚拟地址，它需要经过 MMU 的地址转换变为一个物理地址，再通过它来访问物理内存；而 M 特权级的访存地址，我们可设定是内存的物理地址。
18. 我们采用分页管理，单个页面的大小设置为 4KiB ，每个虚拟页面和物理页帧都对齐到这个页面大小，也就是说虚拟/物理地址区间 [0,4KiB) 为第 0 个虚拟页面/物理页帧，而 [4KiB,8KiB) 为第 1 个，以此类推。
19. 4KiB需要用 12 位字节地址来表示，因此虚拟地址和物理地址都被分成两部分：它们的低 12 位，即 [11:0] 被称为 页内偏移 (Page Offset) ，它描述一个地址指向的字节在它所在页面中的相对位置。而虚拟地址的高 27 位，即 [38:12] 为它的虚拟页号 VPN，同理物理地址的高 44 位，即 [55:12] 为它的物理页号 PPN，页号可以用来定位一个虚拟/物理地址属于哪一个虚拟页面/物理页帧。
20. 地址转换是以页为单位进行的，在地址转换的前后地址的页内偏移部分不变。可以认为 MMU 只是从虚拟地址中取出 27 位虚拟页号，在页表中查到其对应的物理页号（如果存在的话），最后将得到的44位的物理页号与虚拟地址的12位页内偏移依序拼接到一起就变成了56位的物理地址。
21. SV39 分页模式规定 64 位虚拟地址的 [63:39] 这 25 位必须和第 38 位相同，否则 MMU 会直接认定它是一个不合法的虚拟地址。通过这个检查之后 MMU 再取出低 39 位尝试将其转化为一个 56 位的物理地址。
22. 也就是说，所有 264 个虚拟地址中，只有最低的 256GiB （当第 38 位为 0 时）以及最高的 256GiB（当第 38 位为 1 时）是可能通过 MMU 检查的。当我们写软件代码的时候，一个地址的位宽毋庸置疑就是 64 位，我们要清楚可用的只有最高和最低这两部分
23. 有更多的标志位，物理页号和全部的标志位以某种固定的格式保存在一个结构体中，它被称为 页表项 (PTE, Page Table Entry) ，是利用虚拟页号在页表中查到的结果。
24. 页表项，其中 [53:10] 这 44 位是物理页号，最低的 8 位 [7:0] 则是标志位，它们的含义如下（请注意，为方便说明，下文我们用 页表项的对应虚拟页面 来表示索引到一个页表项的虚拟页号对应的虚拟页面）：
* V(Valid)：仅当位 V 为 1 时，页表项才是合法的；
* R(Read)/W(Write)/X(eXecute)：分别控制索引到这个页表项的对应虚拟页面是否允许读/写/执行；
* U(User)：控制索引到这个页表项的对应虚拟页面是否在 CPU 处于 U 特权级的情况下是否被允许访问；
* G：暂且不理会；
* A(Accessed)：处理器记录自从页表项上的这一位被清零之后，页表项的对应虚拟页面是否被访问过；
* D(Dirty)：处理器记录自从页表项上的这一位被清零之后，页表项的对应虚拟页面是否被修改过。
25. 除了 G 外的上述位可以被操作系统设置，只有 A 位和 D 位会被处理器动态地直接设置为 1，表示对应的页被访问过或修过
26. 27 位的虚拟页号可以看成一个长度 n=3 的字符串，字符集为 α={0,1,2,...,511} ，因为每一位字符都由 9 个比特组成。而我们也不再维护所谓字符串的计数，而是要找到字符串（虚拟页号）对应的页表项。因此，每个叶节点都需要保存 512 个 8 字节的页表项，一共正好 4KiB ，可以直接放在一个物理页帧内。这种页表实现被称为 多级页表 (Multi-Level Page-Table) 。由于 SV39 中虚拟页号被分为三级 页索引 (Page Index) ，因此这是一种三级页表。
27. 非叶节点（页目录表，非末级页表）的表项标志位含义和叶节点（页表，末级页表）相比有一些不同：
* 当 V 为 0 的时候，代表当前指针是一个空指针，无法走向下一级节点，即该页表项对应的虚拟地址范围是无效的；
* 只有当 V 为1 且 R/W/X 均为 0 时，表示是一个合法的页目录表项，其包含的指针会指向下一级的页表；
* 注意: 当 V 为1 且 R/W/X 不全为 0 时，表示是一个合法的页表项，其包含了虚地址对应的物理页号。

## 2022/7/26

仔细看test5代码，给所有的代码都按理解打上了注释，到目前为止的代码已经完全弄懂了。

## 2022/7/26

写了test5的实验。明天开test6.
