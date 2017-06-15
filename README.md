# CSharp-Code-Suggestion
The content comes from the book "Writing High Quality Code: Improving 157 Tips for C # Programs".

---
#### 建议1：正确的操作字符串
* 确保尽量少的装箱
* 避免分配额外的内存空间
* 基本类型转换至字符串类型尽量使用它们的 .ToString( ) 方法 &nbsp;&nbsp;&nbsp;[ 它们通常是直接操作内存来完成类型之间的转换的 ]
* 尽可能使用 StringBuilder 类进行字符串拼接
* 使用 string.format( ) 方法进行 StringBuilder 简化拼接操作

#### 建议2：使用默认的转型方法
* 使用类型的转换运算符 [ 包括隐式转换和显示转换(贴标签) ]
* 使用类型内置的转换方法 [ 包括Parse(),TryParse()或者ToString(),ToDouble(),ToDateTime()等方法 ]
* 使用帮助类提供的方法 [ 包括System.Convert类、System.BitConvert类 ]
* 使用CLR支持的转型 [ 实际上就是基类和子类之间的相互转换 ]

#### 建议3：区别对待强制类型与as和is
* as操作符永远不会抛出异常 [ 避免引发异常带来效率问题 ]

#### 建议4：TryParse比Parse好
* Parse会引发异常,引发及捕获异常时会对性能造成损耗

