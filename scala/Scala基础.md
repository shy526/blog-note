# Scala 基础

## 基础关键字
- `val`关键字
  - `val x=1;`
    - 声明一个常量
- `var` 关键字
  - `var x;`
    - 定义一个变量
- `:`
  - `var x :Int`
    - 定义一个int型的变量
  - 明确数据类型
  - 省略时会自动推倒 类似 C++`auto`关键字

- `{}`块
  - `println({var x=2; x*2;})`
  - 独立的代码块
    - 有其独立作用域
  - 类似 匿名内部类 不同的是不需要显式不可变或隐式不可变
- `lambda`匿名函数
  - `val add=(x:Int,y:Int)=>x+y`
    -  定义个匿名函数
  - 类似 c++指针 指向 方法的地址

- `def(参数列表)[(参数列表)...]:返回类型= [{执行语句}|执行语句(单行)]`
  - `def add(a:Int,b:Int)(y:Int): Int =(a+b)*y;`
    - 定一个方法
  - 支持多个参数列表
  - 不用显式的`return`关键字
  - 支持`Unit`
    - 与`void`类似
- `class`关键字
  - `class Point(x:Int,y:Int)`
  - 定义一个类和一个构造函数

- `case` 关键字
  - `case class Point(x:Int,y:Int)`
    - 定义一个不可变的类
  - 特性 类似 java String类
  -  创建实例时可以省略`new`关键字

- `object`关键字
  - 自动添加`static`使其变为静态变量,或静态方法
  - `def main(args: Array[String])`
    - main 程序入口 采取一个参数为字符串数组

- `trait`
  - 特征
  - 类似抽象类 可以提供默认实现或者不实现
  - `class` 通过 `extends`关键字继承`trait`特征
    - 继承方法未实现 需要使用`override` 关键字修饰 重写

## 类型
- `Any`
  - 顶级父类
  - 定义了某些通用的方法
  - 与java Object类 类似
  - 拥有两个直接子类
    - `AnyVal`
    - `AnyRef`
- `AnyVal`
  - 定义了9种基本类型
  - `Double`,`Float`,`Long`,`Int`,`Short`,`Byte`,`Char`,`Unit`,`Boolean`
    - `Unit`:`()`可以用空括号声明
      - 没有任何意思
- `AnyRef`
  - 定义引用类型
  - 每一个自定义类型都是`AnyRef`的子类
  - 在java环境中运行则对应`java.lang.Object`
- `Nothing`
  - 是所有类型的子类型
    - 没有价值
  - `Null`
    - 所有引用类型的子类型
      - 主要为其他JVM语言预留
- 所有数值类型都是对象

```scala
def main(args: Array[String]) {
    var list:List[Any]= List(   "字符串",
      111,
      'c',
      true,
      ( ) => "void",
      111L,
      1.0F,
      2.0D
    )
  list.foreach(value=>println(value))
}
```
### 隐式转换
- Byte->Short->Int->Long->Long->Float->Double
- Char->Int
> 不可逆

## 类

- 类名驼峰命名法首字母大写
- 默认提供一个构造函数不带参数
- 构造函数可以提供默认值
  - 例
```scala
class User2(var name:String="tom" ,var age:Int=18){
  def  js()={
    println(name+":"+age)
  }
}
new User2().js();
new User2("k").js()
new User2(age=40).js()
```
- get/set

```scala
class User{
   private var _name:String="tom"
   private var _age:Int=17;

   //get
   def name=_name;
   def age=_age;
   //set
   def name_=(newValue:String)=_name=newValue;
   def age_=(newValue:Int)=_age=newValue;

   override def toString = s"User3($name, $age)"
 }
```

## trait特性
- 泛型抽象方法
  - 例
```Scala
//定义泛型特性
trait Iterator[A]{
   def hasNext:Boolean;
   def next():A
 }
 //继承特性
 class IntIterator(to:Int) extends Iterator[Int]{
   private var current=0
   //重写抽象方法
   override def hasNext = current<to

   override def next() = {
     if (hasNext){
       val t=current;
       current+=1;
       t
     }else 0
   }
 }
 var xx=new IntIterator(3);
 println(xx.next())
 println(xx.next())
 println(xx.next())
 println(xx.next())
```
- 混入
```Scala
//定义一个抽象类
abstract  class A{
  val message:String;
}

//定义类B继承A
class  B extends A{
  val message="我是类b"
}

//定义一个特性C 继承 A
trait C extends A{
  def loudMessage=message.toUpperCase();
}

//定义类D继承B 支持特性 c
class D extends B with C
val d=new D;
println(d.message);
println(d.loudMessage)
  // with 关键词不能单个使用 extends 则可以为类或特性
//class E with B;
class BB;
class BBB;
  // extends 不支持多继承 只能拥有一个父类
 //class E extends BB extends BBB
//class E extends BB,BBB
trait CC;
trait CCC;
// 特性可以继承,同时也可以混入其他特性
trait  CCCC extends CCC with CC
// with 支持多个 混入
class E extends BB with CC with CCC ;

```

- 实例Scala设计 一个迭代器实例

```Scala
//提供迭代器实现方法
class StringIterator(s:String) extends AbsIterator{
  override type T = Char
  private var i=0;
  override def hasNext = i<s.length
  override def next() = {
    val ch=s charAt i
    i+=1;
    ch
  }
}

//提供一个遍历的字符串的特质
trait  RichIterator extends AbsIterator{
  def foreach(f:T=>Unit):Unit={
    while (hasNext) f(next())
  }
}
  //混入
  class RichStringIter extends StringIterator("Scala") with RichIterator;
 val richStringIter=new RichStringIter;
 richStringIter foreach println
```
> 感叹一下Scala神奇的语法


