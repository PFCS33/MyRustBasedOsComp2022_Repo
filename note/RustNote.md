# 1.Rust语言

* 目标：构建高效且可靠的软件
  * performance：没有运行时和垃圾收集器。
    * 代码运行速度快，内存使用效率高。可用来开发对性能要求高的服务
  * reliability：用类型系统和所有权模型，来确保内存安全性和线程安全性
    * 在编译时消除各自潜在的问题
  * productivity:
    * 有丰富的文档、友好的编译器（提供有用的错误信息）和一流的工具集
    * 包括集成的包管理器和构建工具、支持各种编辑器的代码自动补全和类型查看功能、代码 自动格式化工具等

# 2.Cargo

* 是 Rust 的<u>构建系统和包管理器</u>

  * 它可以为你处理很多任务，比如构建代码、下载 dependencies 并编译这些库

* `cargo new project_name`

  * 创建一个cargo管理的项目，结构如下

    ![image-20221103194203182](C:\Users\11731\AppData\Roaming\Typora\typora-user-images\image-20221103194203182.png)

* `cargo build`
  * 在project根目录下调用命令
  * 编译，并在`./target∕debug∕`下生成可执行文件(debug模式)
* `cargo build --release`
  * 在 `debug` 模式下，Rust 编译器不会做任何的优化，只为了尽快的编译完成，让你的开发流程更加顺畅。
  * 添加 `--release` 来编译生成高性能代码
* `cargo run`
  * 在project根目录下调用命令
  * Cargo 若发现文件并没有被改变，就不会重新编译，而是直接运行可执行文件。如果修改了源文件的话，Cargo 会在运行之前重新build项目,在运行可执行文件
* `cargo check`
  * 在project根目录下调用命令
  * 该命令快速检查代码是否可以编译，不产生可执行文件

# 3.基本语法

## 1.关键字

* Rust 语言有一组保留的 关键字（keywords），
* 就像大部分语言一样，它们只能由语言本身使用。你不能使用这些关键字作为变量或函数的名称。大部分关键字有特殊的意义，你将在 Rust 程序中使用它们完成各种任务；
* 一些关键字目前没有相应的功能，是为将来可能添加的功能保留的。可以在附录 A 中找到关键字的列表。

## 2.变量绑定

* 使用 `let` 进行变量绑定 

  * `let x = 17;`

* **隐式类型**绑定: 可以不给出变量类型，由编译器根据上下文推断类型，此处 x 为 i32 类型

  * 编译器不一定总能成功推断类型（或者推断的结果不是用户想要的类型）
  * 此时可以**显式指定类型**：
    * `let x: i16 = 17;`

* **变量可变性**

  * 默认情况下，变量是<u>不可变</u>的

  * 如果要让变量可变，需要使用 `mut` 修饰

    ```rust
    let x = 5;
    x += 1; // error: re-assignment of immutable variable x
    let mut y = 5;
    y += 1; // OK!
    ```

* **变量隐藏** shadowing

  * 可以定义(`let`)一个与之前变量<u>同名的新变量</u>

    * 第二个变量”遮蔽” 第一个变量
      * 此时任何使用该变量名的行为中，都会视为是在使用第二个变量
      * 直到第二个变量自己也被隐藏或第二个变量的作用域结束（可重复使用`let`关键字来多次隐藏）
    * 可以让同一含义<u>不同类型</u>的东西使用<u>同一个变量名</u>

    ```rust
    let x = 17;
    let y = 53;
    // x is not mutable, but we're able to re-bind it
    let x = "Shadowed!";
    ```

    ```rust
    let mut cost = String::new();
    std::io::stdin().read_line(&mut cost).unwrap();
    let cost: f64 = cost.trim().parse().unwrap();
    ```

## 3.常量 constants

* 使用`const` 关键字声明

  * 必须注明值的类型
  * 值不允许改变

  ```rust
  const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
  ```

* 编译时常数，在<u>编译时</u>确定具体值

  * 只能被设置为<u>常量表达式</u>，而不可以是其他任何只能在运行时计算出的值

* 可以在<u>任何作用域</u>中声明，包括<u>全局作用域</u>

* 在声明它的作用域之中，常量在<u>整个程序生命周期</u>中都有效。

## 4.表达式

* 几乎所有东西都是表达式 (expressions)

  * 会返回一个值 (value) 作为结果
  * 例外：变量绑定`let` 语句不是表达式

* 单位元 unit 类型，表示空，写作 `()`

  * 类型 `()` 只有一个可取的值：`()`
  * 可以作为一个值用来占位，但是完全**不占用**任何内存
  * 是<u>默认的返回类型</u>

* 可以在表达式后加分号 `;` 来舍弃它的值，这时它返回 `()`

  * 因此，如果一个函数以分号结尾，它返回 `()`

    ```rust
    fn foo() -> i32 { 5 }	//return 5
    fn bar() -> () { () }	//return ()
    fn baz() -> () { 5; }	//return ()
    fn qux() { 5; }	//default return ()
    ```

* 由于基本上所有东西都是表达式，因此都可以绑定到变量 (作为右值)

  ```rust
  let mut x = -5;
  
  let y = if x > 0 {
  "greater"
  } else {
  "less"
  };
  
  let z = loop {
  x += 10;
  if x > 5 {
  break x;
  }
  };
  ```

## 5.语句

* 对于 Rust 语言而言，这种基于语句（statement）和表达式（expression）的方式是非常重要的，你需要能明确的区分这两个概念
* 语句只是完成了一个具体的操作，但是并没有返回值
  * 必须加 `;` 结尾
  * 不能作为右值

## 6.数据类型

* Rust 是 **静态类型**（statically typed）语言，也就是说在<u>编译时</u>就必须知道所有变量的类型

  * 可以定义变量时不指定类型
    * Rust 编译器很聪明，它可以根据变量的值和上下文中的使用方式来自动推导出变量的类型
  * 当<u>多种类型均有可能</u>时，<u>必须增加类型注解</u>，否则编译不通过

* 在 Rust 中，每一个值都属于某一个数据类型，这告诉 Rust 它被指定为何种数据，以便明确数据处理方式

  * 两类数据类型子集：标量（scalar）和复合（compound）

  * 基本类型：往往是一个最小化原子类型，无法解构为其它类型(一般意义上来说)

    ![image-20221104014723246](C:\Users\11731\AppData\Roaming\Typora\typora-user-images\image-20221104014723246.png)

### 6.1 **标量类型**

* 代表一个单独的值
* 有四种基本的标量类型：<u>整型、浮点型、布尔类型和字符</u>类型

#### 1）整型

* 有不同的大小和符号属性

* `isize` 和 `usize` 类型依赖运行程序的计算机架构：64 位架构上它们是 64 位的， 32 位架构上它们是 32 位的

  ![image-20221104014851956](C:\Users\11731\AppData\Roaming\Typora\typora-user-images\image-20221104014851956.png)

* 字面值 (literals) 

  * 可在末尾后缀类型名
    *  写为 `10i8`、`10u16`、`10.0f32`、`10usize` 等
  * 如果不指定类型，则默认整数为 `i32`，浮点数为 `f64`
  * 允许使用 _ 做为分隔符以方便读数`98_222`

#### 2）浮点型

* Rust 的浮点数类型是 `f32` 和 `f64` 。默认类型是 `f64`

* 浮点数采用 IEEE-754 标准表示

* 综合举例

  ```rust
  fn main() {
    // 编译器会进行自动推导，给予twenty i32的类型
    let twenty = 20;
    // 类型标注
    let twenty_one: i32 = 21;
    // 通过类型后缀的方式进行类型标注：22是i32类型
    let twenty_two = 22i32;
  
    // 只有同样类型，才能运算
    let addition = twenty + twenty_one + twenty_two;
    println!("{} + {} + {} = {}", twenty, twenty_one, twenty_two, addition);
  
    // 对于较长的数字，可以用_进行分割，提升可读性
    let one_million: i64 = 1_000_000;
    println!("{}", one_million.pow(2));
  
    // 定义一个f32数组，其中42.0会自动被推导为f32类型
    let forty_twos = [
      42.0,
      42f32,
      42.0_f32,
    ];
  
    // 打印数组中第一个值，并控制小数位为2位
    println!("{:.2}", forty_twos[0]);
  }
  ```

#### 3）拓展

* **类型转换必须是显式的**. Rust 永远也不会偷偷把你的 16bit 整数转换成 32bit 整数

* **Rust 的数值上可以使用方法**. 例如你可以用以下方法来将 `13.14` 取整：`13.14_f32.round()`，在这里我们使用了类型后缀，因为编译器需要知道 `13.14` 的具体类型

* 数字运算

  * Rust 中的所有数字类型，都支持基本数学运算：加法、减法、乘法、除法和取余

    ```rust
    fn main() {
        // 加法
        let sum = 5 + 10;
    
        // 减法
        let difference = 95.5 - 4.3;
    
        // 乘法
        let product = 4 * 30;
    
        // 除法
        let quotient = 56.7 / 32.2;
    
        // 求余
        let remainder = 43 % 5;
    }	//这些语句中的每个表达式都使用了数学运算符，并且计算结果为一个值，然后绑定到一个变量上
    ```

* 位运算：基本上和其他语言一样

* 有理数和复数：未包含在标准库中

  * 好在社区已经开发出高质量的 Rust 数值库：[num](https://crates.io/crates/num)

#### 4）布尔型

* 使用 `bool` 声明
* 有两个可能的值：`true` 和 `false`

#### 5）字符类型

* 使用 `char` 声明

* 用单引号`''`声明 `char` 字面量

* 采用Unicode 编码，大小为 4 bytes

  * 所以人直觉上的 “字符” 可能与 Rust 中的 `char` 并不符合

  ```rust
  fn main() {
      let c = 'z';
      let z: char = 'ℤ'; // with explicit type annotation
      let heart_eyed_cat = '😻';
  }
  ```

### 6.2 复合类型

* Rust 有两个原生的复合类型：元组（tuple）和数组（array）

#### 1）元组类型

* <u>固定长度的、有序的、异构</u>的列表类型

  * 长度固定：一旦声明，其长度不会增大或缩小
  * 异构：将多个不同类型的值，组合进一个复合类型的主要方式

* 使用包含在圆括号`()`中的逗号`,`分隔的值列表, 来创建一个元组

  ```rust
  fn main() {
      let tup: (i32, f64, u8) = (500, 6.4, 1);
  }	//可以不声明tup类型，让编译器猜
  ```

* 可以使用点号 `.`后跟值的索引（下标）来直接访问元组分量，例如 `tup.0`

* 解构

  * 因为元组是一个单独的复合元素。为了从元组中获取单个值，可以使用 `let` , 模式匹配（pattern matching）来解构（destructure）元组值

    ```rust
    fn main() {
        let tup = (500, 6.4, 1);
    	let (a, b, c) = (72, 'H', 5.1);
        let (x, y, z) = tup;
        println!("The value of y is: {y}");
    }
    ```

* 不带任何值的元组有个特殊的名称，叫做 **单元（unit）** 元组。这种值以及对应的类型都写作 `()`，表示空值或空的返回类型。

#### 2) 数组类型

* <u>固定长度的，有序的，同类的</u>列表类型

  * 数组中的每个元素的类型必须相同
  * 数组长度是固定的

* 将数组的值写成在方括号 `[]` 内，用逗号 `,` 分隔, 创建一个数组

  * 数组的类型声明： `[T; N]` , T 为元素类型，N 为元素数量

    ```rust
    let a: [i32; 5] = [1, 2, 3, 4, 5];
    ```

  * 还可以通过在 `[]` 中指定初始值, 加`;`, 再加元素个数的方式, 来创建一个每个元素都为相同值的数组

    ```rust
    let a = [3; 5];
    ```

* 用 `[]` 来访问数组元素，数组下标从 0 开始

* 无效的数组元素访问处理

  * 运行时 (runtime) 访问数组元素，会检查是否越界。
  * 如果越界，导致 **运行时** 错误，会程序带着错误信息退出，并且没有执行最后的 `println!` 语句
    * 通过立即退出而不是允许内存访问并继续执行，Rust 让你避开此类错误。

* 切片：

  * 类型的形式为 &[T] ，T为数据类型
  * 表示引用数组中的一部分所形成的视图，不能直接创建，需要从别的变量借用 (borrow)

#### 3）字符串

* Rust 有两种字符串：String 和 &str

* String 是在堆上分配空间、可以增长的字符序列

* &str 是 String 的切片类型

  * 形如 "foo" 的字符串字面值都是 &str 类型的

  ```rust
  let s: &str = "galaxy";
  let s2: String = "galaxy".to_string(); 	//显示类型转换
  let s3: String = String::from("galaxy");
  let s4: &str = &s3;
  ```

#### 4）向量 Vec < T >

* 标准库提供的类型，可直接使用。

  * Vec 是分配在堆上的、可增长的数组

* 表示泛型，使用时代入实际的类型

* 使用 Vec::new() 或 vec! 宏来创建 Vec

  ```rust
  // Explicit typing
  let v0: Vec<i32> = Vec::new();
  // v1 and v2 are equal
  let mut v1 = Vec::new();
  v1.push(1);
  v1.push(2);
  v1.push(3);
  let v2 = vec![1, 2, 3];
  // v3 and v4 are equal
  let v3 = vec![0; 4];
  let v4 = vec![0, 0, 0, 0];
  let v2 = vec![1, 2, 3];
  let x = v2[2]; // 3
  ```

  * 向量可以像数组一样使用 [] 来访问元素
    * 若用变量作为下标，必须使用 usize 类型的值，因为 usize 保证和指针是一样长度的
    * 其他类型要显式转换成 usize

## 7.函数

* 函数名和变量名使用 蛇形命名法 (snake case)

* 函数的位置可以随便放，Rust 不关心我们在哪里定义了函数，只要有定义即可

* 函数定义：

  ```rust
  fn foo(x: T, y: U, z: V) -> T {
  // ...
  }
  ```

  * Rust 是强类型语言，必须显式定义函数的<u>参数和返回值的类型</u>
    * 实际上编译器是可以推断函数的参数和返回值的类型的，但是 Rust 的设计者认为显式指定是 一种更好的实践。
    * 而且，在知道函数需要什么类型后，编译器就能够给出更有用的错误消息。

* 函数返回

  * 在 Rust 中，函数的返回值，等同于函数体<u>最后一个表达式的值</u>

  * 使用 `return` 关键字和指定值，可从函数中提前返回；

    * 但大部分函数隐式的返回最后的表达式

  * 若没有显示定义返回值类型，则默认为`()`

    ```rust
    fn main() {
        let x = plus_or_minus(5);
    
        println!("The value of x is: {}", x);
    }
    ```

## 8. 控制流

* if

  * 格式

    * 与 C++ 不同，条件部分不需要用小括号括起来
    * 整个条件语句是当做一个表达式来求值的，因此<u>每个分支都</u>必须是<u>相同类型的表达式</u>
      * 代码块的值是其最后一个表达式的值
      * 当然，如果作为普通的条件语句来使用的话，可以令类型是 ()

    ```rust
    if x > 0 {
    10
    } else if x == 0 {
    0
    } else {
    println!("Not greater than zero!");
    -10
    }
    ```

  * 条件 **必须** 是 `bool` 值

    * Rust 并不会尝试自动地将非布尔值转换为布尔值

  * 由于 `if` 是一个表达式，我们可以在 `let` 语句的右侧使用它

    ```rust
    fn main() {
        let condition = true;
        let number = if condition { 5 } else { 6 };
    
        println!("The value of number is: {number}");
    }
    ```

* 循环

  * while

    * while 的用法与 C++ 相同，只是条件部分不需要用小括号括起来

      ```rust
      let mut x = 0;
      while x < 100 {
      x += 1;
      println!("x: {}", x);
      }
      ```

  * loop

    * `loop` 关键字. 告诉 Rust 一遍又一遍地执行一段代码直到你明确要求停止 `break` 

      * 相当于 while true

    * `break` 语句可以返回一个值，作为整个循环的求值结果

      * 另外两种循环没 有这个功能

      ```rust
      let mut x = 0;
      let y = loop {
      x += 1;
      if x * x >= 100 {
      break x;
      }
      };
      ```

    * 循环标签：在多个循环之间消除歧义

      * 你可以选择在一个循环上指定一个 **循环标签**（*loop label*），然后将标签与 `break` 或 `continue` 一起使用

  * for：

    * 使用迭代器 (iterators) 表达式，遍历集合中的元素

      * `n..m` 创建一个从 n 到 m 半闭半开区间的迭代器。
      * `n..=m` 创建一个从 n 到 m 闭区间的迭代器。
      * 很多数据结构可以当做迭代器来使用，比如数组、切片，Vec 等等

      ```rust
      // Loops from 0 to 9.
      for x in 0..10 {
      println!("{}", x);
      }
      let xs = [0, 1, 2, 3, 4];
      // Loop through elements in a slice of `xs`.
      for x in &xs {
      println!("{}", x);
      }
      ```

  * 匹配语句

    * 由一个表达式 (x) 和一组 value => expression 的分支语句组成
    * 整个匹配语句被视为一个表达式来求值
      * 与 if 类似，所有分支都必须是同样类型的值
    * 下划线 (_) 用于捕捉所有情况
    * 高级
      * 匹配的表达式可以是任意表达式，包括元组和函数调用。 
        * 构成模式 (patterns)。 
        * 匹配可以绑定变量，_ 用来忽略不需要的部分
      * 为了通过编译，必须写穷尽的匹配模式。 
      * 可以用 if 来限制匹配的条件

    ```rust
    let x = 3;
    match x {
    1 => println!("one fish"), // <- comma required
    2 => {
    println!("two fish");
    println!("two fish");
    }, // <- comma optional when using braces
    _ => println!("no fish for you"), // "otherwise" case
    }
    ```

## 9.代码风格

* 名字 

  * 骆驼形式（CamelCase）：类型名 
  * 蛇形形式（snake_case）：变量名、函数名 

* 缩进与空白 

  * Rust 的缩进用 4 个空格字符。 
  * 符号前后的空格操作符
    * 前后各有一个空格，例如 x + 1。 
    * 分隔符后面有一个空格，例如 f(x, 1)。 

* 注释之道 

  * 写注释 
  * 写有意义的注释 
  * 尽量用英文写注释

  




