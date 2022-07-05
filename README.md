# 2022年开源操作系统训练营日程表
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
