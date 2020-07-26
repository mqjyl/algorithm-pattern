# 强制类型转换

C语言中的强制类型转换（Type Cast）有显式和隐式两种，显式一般就是直接用小括号强制转换：Type\(Value\)或\(Type\)Value。隐式转换存在隐式截断的问题。

C++对C兼容，所以上述方式的类型转换是可以的，但是有时候会有问题，所以C++提供了四个强制类型转换的关键字：static\_cast，const\_cast，reinterpret\_cast 和 dynamic\_cast。

## ✏ static\_cast



