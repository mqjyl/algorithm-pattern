# functional

仿函数（Functor）又称为[函数对象](../advanced-c++/function-object.md)（Function Object），是一个能行使函数功能的类。 在`<functional>`头文件中定义了如下三类仿函数：

## :pencil2: 1、**算术类仿函数**

| 操作 | 仿函数             |
| -- | --------------- |
| 加  | `plus<T>`       |
| 减  | `minus<T>`      |
| 乘  | `multiplies<T>` |
| 除  | `divides<T>`    |
| 取模 | `modulus<T>`    |
| 取反 | `negate<T>`     |

**定义：**

```cpp
template< class T >
struct plus;  // (C++14 前)
template< class T = void >
struct plus;  // (C++14 起)

T operator()( const T& lhs, const T& rhs ) const;           // (C++14 前)
constexpr T operator()( const T& lhs, const T& rhs ) const; // (C++14 起)
```
