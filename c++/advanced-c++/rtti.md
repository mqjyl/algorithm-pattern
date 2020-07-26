# RTTI技术

`RTTI(Run-Time Type Information)`，运行时类型检查的英文缩写，它提供了运行时确定对象类型的方法。面向对象的编程语言，像`C++`，`Java`，`delphi`都提供了对`RTTI`的支持。

运行时类型判定有两种形式：（1）传统的`RTTI`;（2）反射reflection机制。`RTTI`的初期想法非常简单：当有一个指向基础型别（父类）的 `reference`（引用）时，`RTTI`机制让你找出其所指的确切型别，不过当拓展到`java.lang.reflection`的时候，展现了全新的功能。相比较而言，Java的反射机制功能比C++的`RTTI`更完整一些， 一般不要认为Java的反射就是指 `java.lang.reflect` 这个包提供的工具类和接口。其实Java的整个**对象类型系统**，包括所有类的始祖类**Object类**以及每个对象都附带的**Class对象**，都是反射机制的一部分。

