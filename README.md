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
