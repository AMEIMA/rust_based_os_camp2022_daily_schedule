# 2022年开源操作系统训练营日记
## 2022/7/4
今天做了Rust-lang Lab Test based on Rustlings前65个训练，尽量不看rustc的错误提示直接分析代码。

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
10. assert断言宏，可以在第二个参数
