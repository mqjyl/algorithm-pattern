# set/map

## ✏ Map

`map`是一种关联式容器，存储的都是 `pair` 对象，也就是用 `pair` 类模板创建的键值对。 包括 C++ 基本数据类型（`int`、`double` 等）和使用结构体或类自定义的类型。

map是`STL`里的一个模板类，用来存放键值对的数据结构，它的定义如下：

```cpp
template < class Key,                                   //map::key_type
           class T,                                     //map::mapped_type
           class Compare = less<Key>,                   //map::key_compare
           class Alloc = allocator<pair<const Key, T>>  //map::allocator_type
           > class map;
```

* 第1个参数存储了key。
* 第2个参数存储了mapped value。
* 第3个参数是比较函数的函数对象。map用它来判断两个key的大小，并返回bool类型的结果。利用这个函数，map可以确定元素在容器中遵循的顺序以及两个元素键是否相等`(!comp(a, b) && !comp(b, a))`，确保map中没有两个元素可以具有等效键。这里，它的默认值是`less<key>`，定义如下：

```cpp
template <class T> 
struct less {
  bool operator() (const T& x, const T& y) const {return x < y;}
  typedef T first_argument_type;
  typedef T second_argument_type;
  typedef bool result_type;
};
```

* 第4个参数是用来定义存储分配模型的。

### 🖋 1、特点

1. `map` 容器存储的各个键值对，键的值既不能重复也不能被修改。换句话说，`map` 容器中存储的各个键值对不仅键的值独一无二，键的类型也会用 const 修饰，这意味着只要键值对被存储到 `map` 容器中，其键的值将不能再做任何修改。
2.  在使用 map 容器存储多个键值对时，该容器会自动根据各键值对的键的大小，按照既定的规则进行排序。默认情况下，`map` 容器选用`std::less<T>`排序规则（其中 `T` 表示键的数据类型），其会根据键的大小对所有键值对做**升序排序**。当然，根据实际情况的需要，我们可以手动指定 `map` 容器的排序规则，既可以选用 `STL`标准库中提供的其它排序规则（比如`std::greater<T>`），也可以自定义排序规则。

### 🖋 2、创建和初始化

### 🖋 3、成员方法

| 成员方法 | 功能 |
| :--- | :--- |
| `begin()` | 返回指向容器中第一个（注意，是已排好序的第一个）键值对的双向迭代器。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `end()` | 返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器，通常和 `begin()` 结合使用。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `rbegin()` | 返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的反向双向迭代器。 |
| `rend()` | 返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的反向双向迭代器。 |
| `cbegin()` | 和 `begin()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的键值对。 |
| `cend()` | 和 `end()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的键值对。 |
| `crbegin()` | 和 `rbegin()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的键值对。 |
| `crend()` | 和 `rend()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的键值对。 |
| `find(key)` | 在 `map` 容器中查找键为 `key` 的键值对，如果成功找到，则返回指向该键值对的双向迭代器；反之，则返回和 `end()` 方法一样的迭代器。另外，如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `lower_bound(key)` | 返回一个指向当前 `map` 容器中第一个大于或等于 `key` 的键值对的双向迭代器。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `upper_bound(key)` | 返回一个指向当前 `map` 容器中第一个大于 `key` 的键值对的迭代器。如果 `map` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `equal_range(key)` | 该方法返回一个 pair 对象（包含 2 个双向迭代器），其中 `pair.first` 和 `lower_bound()` 方法的返回值等价，`pair.second` 和 `upper_bound()` 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的键为 `key` 的键值对（`map` 容器键值对唯一，因此该范围最多包含一个键值对）。 |
| `empty()`  | 若容器为空，则返回 `true`；否则 `false`。 |
| `size()` | 返回当前 `map` 容器中存有键值对的个数。 |
| `max_size()` | 返回 `map` 容器所能容纳键值对的最大个数，不同的操作系统，其返回值亦不相同。 |
| `operator[]` | `map`容器重载了 `[]` 运算符，只要知道 `map` 容器中某个键值对的键的值，就可以向获取数组中元素那样，通过键直接获取对应的值。 |
| `at(key)` | 找到 `map` 容器中 `key` 键对应的值，如果找不到，该函数会引发 `out_of_range` 异常。 |
| `insert()` | 向 `map` 容器中插入键值对。 |
| `erase()` | 删除 `map` 容器指定位置、指定键（key）值或者指定区域内的键值对。 |
| `swap()` | 交换 2 个 `map` 容器中存储的键值对，这意味着，操作的 2 个键值对的类型必须相同。 |
| `clear()` | 清空 `map` 容器中所有的键值对，即使 `map` 容器的 `size()`为 `0`。 |
| `emplace()` | 在当前 `map` 容器中的指定位置处构造新键值对。其效果和插入键值对一样，但效率更高。 |
| `emplace_hint()` | 在本质上和 `emplace()` 在 `map` 容器中构造新键值对的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示键值对生成位置的迭代器，并作为该方法的第一个参数。 |
| `count(key)` | 在当前 `map` 容器中，查找键为 `key` 的键值对的个数并返回。注意，由于 `map`容器中各键值对的键的值是唯一的，因此该函数的返回值最大为 `1`。 |

### 🖋 4、迭代器

`C++ STL` 标准库为 `map` 容器配备的是双向迭代器（`bidirectional iterator`）。这意味着，`map` 容器迭代器只能进行 `++p`、`p++`、`--p`、`p--`、`*p` 操作，并且迭代器之间只能使用 `==` 或者 `!=` 运算符进行比较。`map` 类模板中还提供了 `find()` 成员方法，它能帮我们查找指定 `key` 值的键值对，如果成功找到，则返回一个指向该键值对的**双向迭代器**；反之，其功能和 `end()` 方法相同。

同时，`map` 类模板中还提供有 `lower_bound(key)` 和 `upper_bound(key)` 成员方法，它们的功能是类似的，唯一的区别在于：

* `lower_bound(key)` 返回的是指向第一个键不小于 `key` 的键值对的迭代器；
* `upper_bound(key)` 返回的是指向第一个键大于 `key` 的键值对的迭代器；

`lower_bound(key)` 和 `upper_bound(key)` 更多用于 `multimap` 容器，在 map 容器中很少用到。

`equal_range(key)` 成员方法可以看做是 `lower_bound(key)` 和 `upper_bound(key)` 的结合体，该方法会返回一个 `pair` 对象，其中的 2 个元素都是迭代器类型，其中 `pair.first` 实际上就是 `lower_bound(key)` 的返回值，而 `pair.second` 则等同于 `upper_bound(key)` 的返回值。显然，`equal_range(key)` 成员方法表示的一个范围，位于此范围中的键值对，其键的值都为 `key`。和 `lower_bound(key)`一样，该方法也更常用于 `multimap` 容器，因为 `map` 容器中各键值对的键的值都是唯一的，因此通过 `map` 容器调用此方法，其返回的范围内最多也只有 `1` 个键值对。

### 🖋 5、 map获取键对应值的几种方法

### 🖋 6、`insert()`

### 🖋 7、 `emplace()`和`emplace_hint()`

### 🖋 8、 `multimap`容器

### 🖋 9、const与static的map

## ✏ Set

`set`也是一种关联式容器，`set`容器中只能存储键，是单纯的键的集合，其中键是不能重复的。

### 🖋 1、特点

1. 所有的元素都会被自动排序。
2. 不能通过迭代器来改变set的值，因为set的值就是键。从语法上讲 set 容器并没有强制对存储元素的类型做 const 修饰，即 set 容器中存储的元素的值是可以修改的。但是，C++ 标准为了防止用户修改容器中元素的值，对所有可能会实现此操作的行为做了限制，使得在正常情况下，用户是无法做到修改 set 容器中元素的值的。

> 如果`set`中允许修改键值的话，那么首先需要删除该键，然后调节平衡，在插入修改后的键值，再调节平衡，如此一来，严重破坏了`set`的结构，导致`iterator`失效，不知道应该指向之前的位置，还是指向改变后的位置。所以`STL`中将`set`的迭代器设置成`const`，不允许修改迭代器的值。

### 🖋 2、创建和初始化

### 🖋 3、成员方法

| 成员方法 | 功能 |
| :--- | :--- |
| `begin()` | 返回指向容器中第一个（注意，是已排好序的第一个）元素的双向迭代器。如果 `set` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `end()` | 返回指向容器最后一个元素（注意，是已排好序的最后一个）所在位置后一个位置的双向迭代器，通常和 `begin()` 结合使用。如果 `set` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `rbegin()` | 返回指向最后一个（注意，是已排好序的最后一个）元素的反向双向迭代器。如果 `set`容器用 `const` 限定，则该方法返回的是 `const` 类型的反向双向迭代器。 |
| `rend()` | 返回指向第一个（注意，是已排好序的第一个）元素所在位置前一个位置的反向双向迭代器。如果 `set` 容器用 `const` 限定，则该方法返回的是 `const` 类型的反向双向迭代器。 |
| `cbegin()` | 和 `begin()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的元素值。 |
| `cend()` | 和 `end()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的元素值。 |
| `crbegin()` | 和 `rbegin()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的元素值。 |
| `crend()` | 和 `rend()` 功能相同，只不过在其基础上，增加了 `const` 属性，不能用于修改容器内存储的元素值。 |
| `find(val)` | 在 `set` 容器中查找值为 `val` 的元素，如果成功找到，则返回指向该元素的双向迭代器；反之，则返回和 `end()` 方法一样的迭代器。另外，如果 `set` 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `lower_bound(val)` | 返回一个指向当前 `set` 容器中第一个大于或等于 `val` 的元素的双向迭代器。如果 set 容器用 `const` 限定，则该方法返回的是 `const` 类型的双向迭代器。 |
| `upper_bound(val)` | 返回一个指向当前 `set` 容器中第一个大于 `val` 的元素的迭代器。如果 `set` 容器用 `const` 限定，则该方法返回的是 `const`类型的双向迭代器。 |
| `equal_range(val)` | 该方法返回一个 `pair` 对象（包含 2 个双向迭代器），其中 `pair.first` 和 `lower_bound()` 方法的返回值等价，`pair.second` 和 `upper_bound()` 方法的返回值等价。也就是说，该方法将返回一个范围，该范围中包含的值为 `val` 的元素（`set` 容器中各个元素是唯一的，因此该范围最多包含一个元素）。 |
| `empty()` | 若容器为空，则返回 `true`；否则 `false`。 |
| `size()` | 返回当前 `set` 容器中存有元素的个数。 |
| `max_size()` | 返回 `set` 容器所能容纳元素的最大个数，不同的操作系统，其返回值亦不相同。 |
| `insert()` | 向 `set` 容器中插入元素。 |
| `erase()` | 删除 `set` 容器中存储的元素。 |
| `swap()` | 交换 2 个 `set` 容器中存储的所有元素。这意味着，操作的 2 个 `set` 容器的类型必须相同。 |
| `clear()` | 清空 `set` 容器中所有的元素，即令 `set` 容器的 `size()` 为 `0`。 |
| `emplace()` | 在当前 `set` 容器中的指定位置直接构造新元素。其效果和 `insert()` 一样，但效率更高。 |
| `emplace_hint()` | 在本质上和 `emplace()` 在 `set` 容器中构造新元素的方式是一样的，不同之处在于，使用者必须为该方法提供一个指示新元素生成位置的迭代器，并作为该方法的第一个参数。 |
| `count(val)` | 在当前 `set` 容器中，查找值为 `val` 的元素的个数，并返回。注意，由于 `set` 容器中各元素的值是唯一的，因此该函数的返回值最大为 `1`。 |

### 🖋 4、迭代器

`set` 容器类模板中未提供 `at()` 成员函数，也未对 `[]` 运算符进行重载。因此，要想访问 `set` 容器中存储的元素，只能借助 `set` 容器的迭代器。

`C++ STL` 标准库为 set 容器配置的迭代器类型为也是双向迭代器。因为 `iter` 迭代器指向的是 `set` 容器存储的某个元素，而不是键值对，因此通过 `*iter` 可以直接获取该迭代器指向的元素的值。除此之外，如果只想遍历 `set` 容器中指定区域内的部分数据，则可以借助 `find()`、`lower_bound()` 以及 `upper_bound()` 实现。通过调用它们，可以获取一个指向指定元素的迭代器。需要特别指出的是，`equal_range(val)` 函数的返回值是一个 `pair` 类型数据，其包含 `2` 个迭代器，表示 `set` 容器中和指定参数 `val` 相等的元素所在的区域，但由于 set 容器中存储的元素各不相等，因此该函数返回的这 `2` 个迭代器所表示的范围中，最多只会包含 `1` 个元素。

虽然 `C++ STL` 标准中，`set` 类模板中包含 `lower_bound()`、`upper_bound()`、`equal_range()` 这 3 个成员函数，但它们更适用于 `multiset` 容器，几乎不会用于操作 `set` 容器。

### 🖋 5、`insert()`

### 🖋6、删除数据 

### 🖋 7、 `emplace()`和`emplace_hint()`

### 🖋 8、 `multiset`容器

##  ✏ 自定义关联式容器的排序规则

 其实在 `STL` 标准库中，本就包含几个可供关联式容器使用的排序规则，如表表示：

| 排序规则 | 功能 |
| :---: | :--- |
| std::less&lt;T&gt;    | 底层采用 &lt; 运算符实现升序排序，各关联式容器默认采用的排序规则。 |
| std::greater&lt;T&gt; | 底层采用 &gt; 运算符实现降序排序，同样适用于各个关联式容器。 |
| std::less\_equal&lt;T&gt; | 底层采用 &lt;= 运算符实现升序排序，多用于 `multimap` 和 `multiset` 容器。 |
| std::greater\_equal&lt;T&gt; | 底层采用 &gt;= 运算符实现降序排序，多用于 `multimap` 和 `multiset` 容器。 |

### 使用[函数对象](../advanced-c++/function-object.md)自定义排序规则

```cpp
class cmp {
public:
    //重载 () 运算符
    bool operator ()(const string &a,const string &b) {
        //按照字符串的长度，做升序排序(即存储的字符串从短到长)
        return  (a.length() < b.length());
    }
};
int main() {
    //创建 set 容器，并使用自定义的 cmp 排序规则
    std::set<string, cmp>myset{"http://c.biancheng.net/stl/",
                               "http://c.biancheng.net/python/",
                               "http://c.biancheng.net/java/"};
    //输出容器中存储的元素
    for (auto iter = myset.begin(); iter != myset.end(); ++iter) {
            cout << *iter << endl;
    }
    return 0;
}
```

## ✏ key的有序性之自定义类型作为`key`

### 🐹 1、简单方法: 重载operator&lt;\(\)操作符

在我们插入时，map会先通过比较函数地函数对象来比对key的大小，然后根据比对结果进行有序存储。c++标准库中，map比较函数的函数对象不可避免地会用到`‘<’`运算，因此一种方法就是直接在自定义类里重载`operator<()`操作符：

```cpp
class Person{
public:
    string name;
    int age;
    Person(string n, int a){
        name = n;
        age = a;
    }
    bool operator < (const Person &p) const //注意这里的两个const
    {
        return (age < p.age) || (age == p.age && name.length() < p.name.length()) ;
    }
};
```

需要注意的是，在重载`operator<(){}`时，无论是参数还是整个函数的`const`都不能少。参照`less`的定义，`less`的参数和函数整体都是`const`，那么被调用的`operator<()`必然也是同等要求。

**还可以以全局函数定义：**

```cpp
bool operator < (const Person &p1, const Person &p2) const //注意这里的两个const
{
    return (p1.age < p2.age) || 
            (p1.age == p2.age && p1.name.length() < p2.name.length()) ;
}
```

当以成员函数的方式重载 &lt; 运算符时，该成员函数必须声明为 const 类型，且参数也必须为 const 类型，至于参数的传值方式是采用按引用传递还是按值传递，都可以（建议采用按引用传递，效率更高）。

**还可以使用友元函数的形式实现：**

```cpp
//类中友元函数的定义
friend bool operator < (const Person &p1, const Person &p2);
//类外部友元函数的具体实现
bool operator < (const Person &p1, const Person &p2) {
    return (p1.age < p2.age) || 
            (p1.age == p2.age && p1.name.length() < p2.name.length()) ;
}
```

### 🐹 2、其它方法：比较函数的函数对象

除了直接重载`operator<()`，我们可以直接自定义比较函数的函数对象。首先简要介绍一下函数对象的概念：在《C++ Primer Plus》里面，函数对象是可以以函数方式与\(\)结合使用的任意对象。这包括函数名、指向函数的指针和重载了`“operator()”`操作符的类对象。基于此，我们提出3种定义方法。 

#### 🍈 2.1、方法1: 利用std::function

它是一种通用、多态、类型安全的函数封装，其实例可以对任何可调用目标实体（包括普通函数、Lambda表达式、函数指针、以及其它函数对象等）进行存储、复制和调用操作，方法如下：

```cpp
#include <iostream>
#include <map>
#include <string>
#include <functional>
using namespace std;

class Person{
public:
    string name;
    int age;
    Person(string n, int a){
        name = n;
        age = a;
    }
};

bool MyCompare(const Person &p1, const Person &p2) {//普通的函数
    return (p1.age < p2.age) || (p1.age == p2.age && p1.name.length() < p2.name.length());
}

int main(int argc, char* argv[]){
    map<Person, int, function<bool(const Person &, const Person &)>> group(MyCompare); //需要在构造函数中指明
    group[Person("Mark", 17)] = 40561;
    group[Person("Andrew",18)] = 40562;
    for ( auto ii = group.begin() ; ii != group.end() ; ii++ )
        cout << ii->first.name 
        << " " << ii->first.age
        << " : " << ii->second
        << endl;
    return 0;
}
```

我们利用`std::function`为`MyCompare()`构建函数实例。初始化时，这个函数实例就会被分配那个指向`MyCompare()`的指针。因此，在对group进行申明时，需要构造函数指明函数实例。

另外，c++11增加了一个新的关键词`decltype`，它可以直接获取自定义哈希函数的类型，并把它作为参数传送。因此，group的声明可以如下修改：

```cpp
map<Person, int, decltype(&MyCompare)> group(MyCompare);
```

#### 🍈 2.2、方法2: 重载operator\(\)的类 <a id="3.1"></a>

利用重载operator\(\)的类，将比较函数打包成可以直接调用的类：

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

class Person{
public:
    string name;
    int age;
    Person(string n, int a){
        name = n;
        age = a;
    }
};

struct MyCompare{  //Function Object
    bool operator()(const Person &p1, const Person &p2) const{
        return (p1.age < p2.age) || (p1.age == p2.age && p1.name.length() < p2.name.length());
    }
};

int main(int argc, char* argv[]){
	map<Person, int, MyCompare> group;
	group[Person("Mark", 17)] = 40561;
    group[Person("Andrew",18)] = 40562;
    for ( auto ii = group.begin() ; ii != group.end() ; ii++ )
        cout << ii->first.name 
        << " " << ii->first.age
        << " : " << ii->second
        << endl;
    return 0;
}

```

值得注意的是，这时group的声明不再需要将函数对象的引用传入构造器里。因为map会追踪类定义，当需要比较时，它可以动态地构造对象并传递数据。

#### 🍈 2.3、方法3: less函数的模板定制 <a id="3.1"></a>

通过map的定义可知，第三个参数的默认值是less。显而易见，less属于模板类。那么，我们可以对它进行模板定制，如下所示：

```cpp
#include <iostream>
#include <map>
#include <string>
using namespace std;

class Person{
public:
    string name;
    int age;
    Person(string n, int a){
        name = n;
        age = a;
    }
};

template <> //function-template-specialization
    struct less<Person>{
    public :
        bool operator()(const Person &p1, const Person &p2) const {
            return (p1.age < p2.age) || (p1.age == p2.age && p1.name.length() < p2.name.length());
        }
};

int main(int argc, char* argv[]){
    map<Person, int> group; //无需指定第三个参数啦
    group[Person("Mark", 17)] = 40561;
    group[Person("Andrew",18)] = 40562;
    for ( auto ii = group.begin() ; ii != group.end() ; ii++ )
        cout << ii->first.name 
        << " " << ii->first.age
        << " : " << ii->second
        << endl;

    return 0;
}
```

