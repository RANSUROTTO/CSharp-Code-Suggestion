# CSharp-Code-Suggestion

### 基本语言要素
---
#### 建议1：正确的操作字符串
* 确保尽量少的装箱
* 避免分配额外的内存空间
* 基本类型转换至字符串类型尽量使用它们的 .ToString( ) 方法 &nbsp;&nbsp;&nbsp;[ 它们通常是直接操作内存来完成类型之间的转换的 ]
* 尽可能使用 StringBuilder 类进行字符串拼接
* 使用 string.format( ) 方法进行 StringBuilder 简化拼接操作

#### 建议2：使用默认的转型方法
* 使用类型的转换运算符 [ 包括隐式转换和显示转换(贴标签) ]
* 使用类型内置的转换方法 [ 包括Parse( ),TryParse( )或者ToString( ),ToDouble( ),ToDateTime( )等方法 ]
* 使用帮助类提供的方法 [ 包括System.Convert类、System.BitConvert类 ]
* 使用CLR支持的转型 [ 实际上就是基类和子类之间的相互转换 ]

#### 建议3：区别对待强制类型与as和is
* as操作符永远不会抛出异常 [ 避免引发异常带来效率问题 ]

#### 建议4：TryParse比Parse好
* Parse会引发异常,引发及捕获异常时会对性能造成损耗

#### 建议5：使用int?来确保值类型也可以为null
* 取值转换赋值为int,不会因为是null而引发异常
* 数据被篡改转型失败后应该保存为null值,而不是默认值

#### 建议6：区别readonly和const的使用方法
* const是一个编译期常量,readonly是一个运行时常量
* const只能修饰基元类型、枚举类型或字符串类型,readonly没有限制
* const天然是static的
* readonly对于值类型变量,值本身不可改变
* readonly对于引用类型变量,引用本身(相当于指针)不可改变

#### 建议7：将0值作为枚举的默认值
* 编译器自动从0值开始计数
* 枚举默认值为0,您如果没有为0值的枚举值,使用也不会引发异常 [ 出乎意外的选项应是不允许的 ]

#### 建议8：避免给枚举类型的元素提供显式的值
> 上一个建议已经讲到如果没有为元素显式赋值,编译器会逐个为元素的值+1
```csharp
//注意以下的代码
enum Week
{
  Monday = 1,
  Tuesday = 2,
  ValueTemp,
  Wednesday = 3,
  Thursday = 4,
  Friday = 5,
  Saturday = 6,
  Sunday = 7
}
```
> 执行时实际会发现 if ( Week.ValueTemp == Week.Wednesday ) 结果为 true ,当编译器发现元素 ValueTemp 时,它会自动在 Tuesday = 2 的基础上 +1,所以实际 ValueTemp 的值和 Wednesday 的值都是 3

#### 建议9：习惯重载运算符
* 构建类时考虑是否可以通过使用 operator 关键字定义静态函数来重载运算符

#### 建议10：创建对象时需要考虑是否实现比较器
* 构建类时考虑是否可以通过实现 IComparable<T> 比较器接口

#### 建议11：区别对待 == 和 Equals
* 一般来说,对于引用类型,我们要定义"值类型相等",应该仅仅去重载 Equals( ) 方法,同时让 "==" 表示"引用性相等"

#### 建议12：重写Equals时也要重写GetHashCode
* 除非考虑到自定义类型会被用作于散列的集合的键值;否则,不建议重写 Equals( ) 方法,因为这会带来一系列问题
* CLR首先会对 GetHashCode( ) 进行比较 [ 如未重写则会调用 object.GetHashCode( ) 方法,会对散列的集合处理产生影响 ]

#### 建议13：为类型输出格式化字符串
* 为类型重写 Object.ToString( ) 方法
* 让类型继承接口 IFormattable ,可以根据需求的变化为类型提供多个格式化器

#### 建议14：正确实现浅拷贝和深拷贝
* 浅拷贝-将对象的所有字段复制到新的对象中.其中值类型字段在副本中修改不会影响源对象的值.而引用类型字段在副本中修改则会影响到源对象本身
* 深拷贝-同样将对象中的所有字段复制到新对象中.不过无论是对象的值类型字段和是引用类型字段,都会被重新创建赋值,对于副本的修改,不会影响到源对象本身
* 浅拷贝过程中,应将字符串看成是值类型 [ 理论上string类型是引用类型 ]
* 建议实现接口ICloneable.Clone( )方法完成浅拷贝,由自己提供额外的方法完成深拷贝

#### 建议15：使用dynamic来简化反射实现
* 建议始终使用dynamic来简化反射实现 [ 除非你愿意牺牲大范围的代码整洁度去写复杂的反射优化 ]


### 集合和LINQ
---
#### 建议16：元素数量可变的情况下不应使用数组
* 数组不应指定过大长度,这会让数组成为一个大对象 [ 大对象在回收过程中会带来效率低的问题 ]
* 在元素数量可变的情况下请考虑ArrayList或List&lt;T&gt;

#### 建议17：多数情况下使用foreach进行循环遍历
* 使用foreach最大简化迭代代码
* foreach会自动将代码置入try-finally块
* 若类型实现了IDispose接口,他会在循环结束后自动调用Dispose方法

#### 建议18：foreach不能代替for
* foreach循环会调用MoveNext方法来遍历元素,在MoveNext方法内部会进行版本检测,当检测到版本号有变动,就会抛出InvalidOperationException异常.所以它不支持循环时对集合进行增删改查

#### 建议19：使用更有效的对象和集合初始化
* FCL3.5之后提供新语法:对象和集合初始化设定项
```csharp
  List<Person> personList = new List<Person>(){
    new Person() { Name='Rose', Age = 19 },
    mike,
    null
  }
```
> 初始化设定项绝不仅是为了对象和集合初始化的方便,它更重要的作用是为了LINQ查询中的匿名类型进行属性初始化

#### 建议20：使用泛型集合代替非泛型集合
* 非泛型集合在进行循环访问,转型或拆箱和装箱操作中对效率影响极大

#### 建议21：选择正确的集合
* 如果集合的数目固定并且不涉及转型,使用数组效率高,否则就使用 List&lt;T&gt; 
> 集合的分类
![list type](https://github.com/ZhengZicong/CSharp-Code-Suggestion/blob/master/image_1.png)<br/>
> FCL集合类图
![fcl list](https://github.com/ZhengZicong/CSharp-Code-Suggestion/blob/master/image_2.png)

#### 建议22：确保集合的线程安全
* 可以通过 Lock( ) 来实现集合在多个线程被操作时保持同步
* 可以使用System.Collections.Concurrent命名空间下实现线程的集合类
* ConcurrentBag&lt;T&gt; 对应 List&lt;T&gt;
* ConcurrentDictionary<TKey,TValue> 对应 Dictionary<TKey,TValue>
* ConcurrentQueue&lt;T&gt; 对应 Queue&lt;T&gt;
* ConcurrentStack&lt;T&gt; 对应 Statck&lt;T&gt;

#### 建议23：避免将List&lt;T&gt;作为自定义集合类的基类
* 如果要实现一个自定义的集合类,不应该以一个FCL集合类为基类
* 实现一个自定义集合类,应扩展相应的泛型接口.FCL集合类应该以组合的形式包含至自定义的集合类

#### 建议24：迭代器应该是只读的
* 不要为迭代器设置可写属性

#### 建议25：谨慎集合属性的可写操作
* 如果类型的属性中有集合属性,将其设置为可写,则会增加抛出异常的几率

#### 建议26：使用匿名类型存储LINQ查询结果
* 使用匿名类代替复杂多变的业务临时类型使代码变的简洁及易于维护
> 匿名类的基本特性
1. 既支持简单类型也支持复杂类型,简单类型必须是一个非空初始值,复杂类型则是一个以new开头的初始化项
2. 匿名类型的属性是只读的,没有属性设置器,它一旦被初始化就不可更改
3. 如果两个匿名类的属性值相同,那么就认为这两个匿名类型相等
4. 匿名类型可以在循环中用作初始化器
5. 匿名类型支持智能感知
6. 匿名类型也可以拥有方法

#### 建议27：在查询中使用Lambda表达式
* LINQ实际上是基于扩展方法和Lambda表达式的,任何LINQ查询都能够通过调用扩展方法和LINQ表达式来代替

#### 建议28：理解延迟求值和主动求值之间的区别
* 使用LINQ求值时,延迟求值能够带来显著的效果提升
* 应细知延迟求值和主动求值的区别,体会两者在应用中带来的结果,避免意想不到的Bug

#### 建议29：区别LINQ查询中的IEnumberable&lt;T&gt;和IQueryable&lt;T&gt;
* 使用本地数据源时采用IEnumberable&lt;T&gt;,远程数据源用IQueryable&lt;T&gt; [ Enumberable中查询参数接受的是Func<>,使用Queryable时接受的参数是Experssion<> ]

#### 建议30：使用LINQ取代集合中的比较器和迭代器
* 实现比较器和迭代器做操作可扩展性太低,对代码侵入性过高.强烈建议你使用LINQ所带来的便捷性

#### 建议31：在LINQ查询中避免不必要的迭代
* 实际编码中,要充分运用 First( ) 和 Take( ) 等方法,为应用带来高效性,而不会让时间浪费在一些无效的迭代中


### 泛型、委托和事件
---
#### 建议32：总是优先考虑泛型
* 无论是泛型类还是泛型方法都同时具备可重用性、类型安全和高效率等特性

#### 建议33：避免在泛型类型中声明静态成员
* 随着你为T指定不同的数据类型,泛型类也会变成不同的数据类型,在它们之间是不共享静态成员的

#### 建议34：为泛型参数设定约束
* 泛型约束让泛型参数具有更多的行为和属性,编码过程中应该始终考虑为泛型参数设定约束 [ 约束使泛型参数称为一个实实在在的"对象",让它具有了我们想要的行为和属性,而不是一个object ]

#### 建议35：使用default为泛型类型变量指定初始值
* 如果不知道泛型具体类型为值类型还是引用类型,要让编译器接收为一个泛型类型参数指定一个初始值,最妥当的办法就是使用default关键字 [ T t = default( T ); ] 

#### 建议36：使用FCL中的委托声明
* 应习惯在代码中使用FCL自带的委托声明来代替自己的委托声明,FCL中每一种委托都代表一类特殊的用途,使用自带委托可以让代码更具有简洁性和标准性

#### 建议37：使用Lambda表达式代替方法和匿名方法
* 使用Lambda或匿名方法可以更好的简化及美化代码 [ 前提是不重用 ]

#### 建议38：小心闭包中的陷阱
```csharp
  //设想以下代码会输出什么
  static void Main(string[] args)
  {
    //我们的意图是让匿名方法(在这里表现为Lambda表达式)接受参数i,并输出
    List<Action> lists = new List<Action>();
    for(int i = 0 ; i < 5 ; i++ )
    {
      Action t = () => { Console.WriteLine(i.ToString()); };
      lists.Add(t);
    }
    foreach(Action t in lists)
    {
      t();
    }
  }
  //你预想的结果可能是: 0 1 2 3 4 
  //而实际输出的是: 5 5 5 5 5
```
> 如果匿名方法引用了某个局部变量,编译器就会自动将该引用提升到该闭包对象中,将会偏离编码时预期的结果

#### 建议39：了解委托的实质
> 理解c#中的委托需要把握两个要点:
1. 委托类似于方法(类型安全)的指针
2. 委托是一个类(或这个类的集合),当对其(一)进行实例化的时候,要将引用方法作为它的构造方法的参数

#### 建议40：使用event关键字为委托施加保护
* event为委托加了保护,保证委托什么时候通知,是委托自己的职责,而不是由调用者决定

#### 建议41：实现标准的事件模型
> 微软为事件模型设定的几个规范
1. 委托类型的名称以EventHandler结束
2. 委托原型返回值为void
3. 委托具有两个参数:sender表示事件触发者,e表示事件参数
4. 事件参数的名称以EventArgs结束

#### 建议42：使用泛型参数兼容泛型接口的不变性
* 让返回值类型返回比声明的类型派生程度更大的类型,叫做"协变"
```csharp
  //例 以下称为协变
  public Employee GetAEmployee(string name)
  {
    Console.WriteLine("我是雇员:{0}",name);
    return new Programemer(){ Name = name }; // Programemer是Employee的子类
  }
```
* 但以下代码将会不成立 [ 编译错误 ]
```csharp
  interface ISalary<T> { void Pay( ); }
  
  class BaseSalary<T> : ISalary<T> { void Pay( ){ Console.WriteLine("pay base salary"); } }
  
  static void Main(string[] args)
  {
    ISalary<Programemer> s = new  ISalary<Programemer>();
    PrintSalary(s);
  }
  
  static void PrintSalary(ISalary<Employee> s)
  {
    s.Pay();
  }
```
> 可以注意到,本建议开头指出的"协变"是针对返回值而言的,但是所举的这个例子却没有体现出"返回值"这个概念
```csharp
  static void PrintSalary<T>(ISalary<T> s)
  {
    s.pay();
  }
```
> 上面这个改动可以正常运行.说明实际上,只要泛型类型参数在一个接口声明中不被用来作为方法的输入参数,我们都可姑且把它看成"返回值"类型的。上面这个改动,是满足"协变"的定义的.

































