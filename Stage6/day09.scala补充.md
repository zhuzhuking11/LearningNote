## 集合计算基础函数

（1）求和
（2）求乘积
（3）最大值
（4）最小值
（5）排序

```scala
package cn.tedu.scalacollection

/**
 * 集合计算基本函数
 */
object CollectionSimpleFunctionTest {
  def main(args: Array[String]): Unit = {
    val list = List(1, 3, 4, 2, 5)
    //    （1）求和
    var sum = 0L
    for (elem <- list) sum += elem
    println(sum)
    println(list.sum)
    //    （2）求乘积
    println(list.product)
    //    （3）最大值
    println(list.max)
    val t2List = List(("a", 1), ("c", 3), ("b", 2), ("d", 4))
    println(t2List.maxBy((t: (String, Int)) => {
      t._2
    }))
    println(t2List.maxBy(_._2))
    //    （4）最小值
    println(list.min)
    //    （5）排序
    val list2 = list.sorted
    println(list2)
    println(t2List.sortBy(_._2)(Ordering[Int].reverse))
    println(list.sortWith((a, b) => {
      a > b
    }))
    println(list.sortWith(_ < _))
  }
}


```

## 集合计算的高阶函数

（1）过滤（filter）
遍历一个集合并从中获取满足指定条件的元素组成一个新的集合
（2）转化/映射（map）
将集合中的每一个元素映射到某一个函数
（3）扁平化
（4）扁平化+映射 注：flatMap 相当于先进行 map 操作，在进行 flatten 操作
集合中的每个元素的子元素映射到某个函数并返回新集合
（5）分组(group)
按照指定的规则对集合的元素进行分组
（6）简化（归约）
（7）折叠

```scala
package cn.tedu.scalacollection

/**
 * 集合计算的高阶函数
 */
object CollectionHightLevelFunctionTest {
  def main(args: Array[String]): Unit = {
    val list = List(1, 2, 3, 4, 5, 6, 7)
    //    （1）过滤
    //    遍历一个集合并从中获取满足指定条件的元素组成一个新的集合
    println(list.filter((a: Int) => {
      a % 2 == 0
    }))
    println(list.filter(_ % 2 != 0))
    //    （2）转化/映射（map）
    //    将集合中的每一个元素映射到某一个函数
    println(list.map(_ * 10))
    //    （3）扁平化
    val list2 = List(List(1, 2, 3), List(4, 5, 6), List(7, 8))
    val list3 = list2(0) ::: list2(1) ::: list2(2)
    println(list3)
    println(list2.flatten)
    //    （4）扁平化+映射 注：flatMap 相当于先进行 map 操作，在进行 flatten 操作
    //    集合中的每个元素的子元素映射到某个函数并返回新集合
    val list4 = List("hello Java", "hello Scala", "Scala Spark")
    list4.flatMap((s: String) => {
      s.split(" ")
    }).foreach(println)
    list4.flatMap(_.split(" ")).foreach(println)
    //    （5）分组(group)
    //    按照指定的规则对集合的元素进行分组
    //    按照首字母将单词进行归类分组
    val list5 = List("hadoop", "hive", "flink", "flume", "scala", "spark", "hbase")
    val wordList: Map[Char, List[String]] = list5.groupBy(_.charAt(0))
    println(wordList)
    //    （6）简化（归约）
    println(list.reduce(_ + _))
    println(list.reduceLeft(_ + _)) //从左往右
    println(list.reduceRight(_ + _)) //从右往左

    val list6 = List(1, 2, 3, 4, 5)
    println(list6.reduce(_ - _)) //-13
    println(list6.reduceLeft(_ - _)) //-13
    println(list6.reduceRight(_ - _)) // 3 ?   (1-(2-(3-(4-5))))

    //    （7）折叠
    println(list6.fold(5)(_ + _)) //初始值为5的折叠
  }
}


```

## 隐式转换

隐式转换是在Scala编译器进行类型匹配时，如果找不到合适的类型，那么隐式转换会让编译器在作用范围内自动推导出来合适的类型。

### 1.隐式值与隐式参数

隐式值是指在定义属性时前面加上implicit。隐式参数是指在定义方法时，方法中的部分参数是由implicit修饰【必须使用柯里化的方式，将隐式参数写在后面的括号中】。隐式转换作用就是：当调用方法时，不必手动传入方法中的隐式参数，Scala会自动在作用域范围内寻找隐式值自动传入。
隐式值和隐式参数注意：
1). 同类型的参数的隐式值只能在作用域内出现一次，同一个作用域内不能定义多个类型一样的隐式值。
2). implicit 关键字必须放在隐式参数定义的开头
3). 一个方法只有一个参数是隐式转换参数时，那么可以直接定义implicit关键字修饰的参数，调用时直接创建类型不传入参数即可。
4). 一个方法如果有多个参数，要实现部分参数的隐式转换,必须使用柯里化这种方式,隐式关键字出现在后面，只能出现一次

```scala
package cn.tedu.scalaimplicit

/**
 * 属性和参数的隐式转换
 */
object ImplicitValTest {
  //定义隐式属性
  implicit val name = "张飞"
  implicit val gender = '男'
  //定义隐式参数
  def sayHi(implicit name:String): Unit ={
    println(s"${name}你好!!!")
  }

  //如果部分参数是隐式参数,隐式参数需要使用柯里化形式单独定义,并且要定义在最后.implicit关键字只写一次.
  def sayHello(age:Int)(implicit name:String, gender:Char): Unit ={
    println(s"name = ${name}, gender = ${gender}, age = ${age}")
  }
  def main(args: Array[String]): Unit = {
    //有隐式参数的方法在调用时如果只有一个参数并且是隐式的直接使用方法名调用,传参即覆盖
    sayHi
    sayHi("刘备")
//    sayHi() //不能写()

    sayHello(18)
  }
}

```

### 2.隐式转换函数

隐式转换函数是使用关键字implicit修饰的方法。当Scala运行时，假设如果A类型变量调用了method()这个方法，发现A类型的变量没有method()方法，而B类型有此method()方法，会在作用域中寻找有没有隐式转换函数将A类型转换成B类型，如果有隐式转换函数，那么A类型就可以调用method()这个方法。
隐式转换函数注意：隐式转换函数只与函数的参数类型和返回类型有关，与函数名称无关，所以作用域内不能有相同的参数类型和返回类型的不同名称隐式转换函数。

```scala
package cn.tedu.scalaimplicit

/**
 * 隐式转换方法
 */
object ImplicitFunTest {
  def main(args: Array[String]): Unit = {
    val pig = Pig("猪猪")
    pig.fly()
  }

  //定义隐式转换方法
  implicit def pigToBird(pig: Pig): Bird = {
    new Bird(pig.name)
  }
}

class Bird(name: String) {
  def fly(): Unit = {
    println(s"${name}飞起来了!!!")
  }
}

case class Pig(val name: String)


```

### 3.隐式类（了解）

使用implicit关键字修饰的类就是隐式类。若一个变量A没有某些方法或者某些变量时，而这个变量A可以调用某些方法或者某些变量时，可以定义一个隐式类，隐式类中定义这些方法或者变量，隐式类中传入A即可。
隐式类注意：
1).隐式类必须定义在类，包对象，伴生对象中。
2).隐式类的构造必须只有一个参数，同一个类，包对象，伴生对象中不能出现同类型构造的隐式类。

```scala
package cn.tedu.scalaimplicit

/**
 * 隐式类
 */
object ImplicitClassTest {
  //隐式类必须在class或者object(包括包对象)中定义.
  implicit class M(m:Monkey){
    def eat(): Unit ={
      println(s"${m.name}在吃饭.")
    }
  }
  def main(args: Array[String]): Unit = {
    val sun = new Monkey("孙悟空")
    sun.eat()
  }
}
class Monkey(var name:String)


```



## WordCount

  

```scala
package cn.tedu

/**
 * WordCount词频统计
 * 读取文本文件中的数据,统计每个单词出现的次数,找到最多的三个单词.
 */
object WordCount {
  def main(args: Array[String]): Unit = {
    //导入IO包
    import io.Source
    //读取文件
    val lines = Source.fromFile("data.txt").getLines.toList
    //拆分单词
    val strings = lines.flatMap(_.split(" "))
    //将单词转化为Tuple2
    val tuples = strings.map((_, 1))
    //按照单词分组后进行统计个数
    val counted = tuples.groupBy(_._1).map(kv => (kv._1,kv._2.length))
    //打印所有统计结果
    counted.foreach(println)
    //排序找到Top3的单词
    val top3 = counted.toList.sortWith(_._2 > _._2).take(3)
    //打印Top3
    println(top3)
  }
}
```

