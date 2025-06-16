# String

## :pencil2: 1、成员函数

| 函数名称                           | 功能                   |
| ------------------------------ | -------------------- |
| 构造函数                           | 产生或复制字符串             |
| 析构函数                           | 销毁字符串                |
| =，`assign`                     | 赋以新值                 |
| `Swap`                         | 交换两个字符串的内容           |
| `+ =`，`append()`，`push_back()` | 添加字符                 |
| `insert ()`                    | 插入字符                 |
| `erase()`                      | 删除字符                 |
| `clear ()`                     | 移除全部字符               |
| `resize ()`                    | 改变字符数量               |
| `replace()`                    | 替换字符                 |
| `+`                            | 串联字符串                |
| ==，！ =，<，<=，>，>=，`compare()`   | 比较字符串内容              |
| `size()`，`length()`            | 返回字符数量               |
| `max_size()`                   | 返回字符的最大可能个数          |
| `empty()`                      | 判断字符串是否为空            |
| `capacity()`                   | 返回重新分配之前的字符容量        |
| `reserve()`                    | 保留内存以存储一定数量的字符       |
| `[]`，`at()`                    | 存取单一字符               |
| `>>`，`getline()`               | 从 stream 中读取某值       |
| `<<`                           | 将值写入 stream          |
| `copy()`                       | 将内容复制为一个 C - string  |
| `c_str()`                      | 将内容以 C - string 形式返回 |
| `data()`                       | 将内容以字符数组形式返回         |
| `substr()`                     | 返回子字符串               |
| `find()`                       | 搜寻某子字符串或字符           |
| `begin()`，`end()`              | 提供正向迭代器支持            |
| `rbegin()`，`rend()`            | 提供逆向迭代器支持            |
| `get_allocator()`              | 返回配置器                |

## :pencil2: 2、创建与初始化



## :pencil2: 3、类型转换



## :pencil2: 4、关于`split`

C++11以前有很多原因不能提供一个通用的`split`，比如说需要考虑`split`以后的结果存储在什么类型的容器中，可以是`vector`、`list`等等包括自定义容器，很难提供一个通用的；再比如说需要`split`的源字符串很大的时候运算的时间可能会很长，所以这个`split`最好是`lazy`的，每次只返回一条结果。

目前发现的史上最优雅的一个实现是这样的：

```cpp
void split(const string& s, vector<string>& tokens, const string& delimiters = " "){
    string::size_type lastPos = s.find_first_not_of(delimiters, 0);
    string::size_type pos = s.find_first_of(delimiters, lastPos);
    while (string::npos != pos || string::npos != lastPos) {
        tokens.push_back(s.substr(lastPos, pos - lastPos));//use emplace_back after C++11
        lastPos = s.find_first_not_of(delimiters, pos);
        pos = s.find_first_of(delimiters, lastPos);
    }
}
```

但是分隔符只能是字符，为字符串时并不能有效，下面提供一种实现支持分割符是字符串的情况：

```cpp
void SplitString(const std::string& s, std::vector<std::string>& tokens, const std::string& c){
    std::string::size_type firstPos, lastPos;
    lastPos = s.find(c);
    firstPos = 0;
    while(std::string::npos != lastPos){
        tokens.push_back(s.substr(firstPos, lastPos - firstPos));
        firstPos = lastPos + c.size();
        lastPos = s.find(c, firstPos);
    }
    if(firstPos != s.length())
        tokens.push_back(s.substr(firstPos));
}
```

从C++11开始，标准库中提供了`regex`，`regex`用来做`split`：

```cpp
std::string text = "Quick brown fox.";
std::regex ws_re("\\s+"); // whitespace
std::vector<std::string> v(
    std::sregex_token_iterator(text.begin(), text.end(), ws_re, -1), 
    std::sregex_token_iterator()
    );
for(auto&& s: v)
    std::cout<<s<<"\n";    
```

C++17提供的`string_view`可以加速上面提到的第一个`split`实现，减少拷贝，性能有不小提升，参看此文： [Speeding Up string\_view String Split Implementation](https://link.zhihu.com/?target=https%3A//www.bfilipek.com/2018/07/string-view-perf-followup.html)。

从C++20开始，标准库中提供了ranges，有专门的split view，只要写str | split(' ')就可以切分字符串，如果要将结果搜集到vector中，可以这样用：

```cpp
string str("hello world test split");
auto sv = str
    | ranges::views::split(' ') 
    | ranges::views::transform([](auto&& i){
        return i | ranges::to<string>(); }) 
    | ranges::to<vector>();
    
for(auto&& s: sv)
    cout<<s<<"\n";
```

其实C语言里面也有一个函数`strtok`用于`char*`的`split`，例如：

```cpp
void split(char *src, const char *separator, char **dest, int *num) {
     char *pNext;
     int count = 0;
     if (src == NULL || strlen(src) == 0)
        return;
     if (separator == NULL || strlen(separator) == 0)
        return;
     pNext = strtok(src,separator);
     while(pNext != NULL) {
          *dest++ = pNext;
          ++count;
         pNext = strtok(NULL,separator);
    }
    *num = count;
}
```

这里要注意的是`strtok`的第一个参数类型是`char*`而不是`const char*`，实际上`strtok`会改变输入的字符串。
