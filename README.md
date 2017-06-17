# CSharp-Code-Suggestion
The content comes from the book "Writing High Quality Code: Improving 157 Tips for C # Programs".

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
* 在元素数量可变的情况下请考虑ArrayList或List<T>

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
* 如果集合的数目固定并且不涉及转型,使用数组效率高,否则就使用List<T>








































