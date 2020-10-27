# 括号匹配问题

### 有效的括号【[链接](https://leetcode-cn.com/problems/valid-parentheses/)】

给定一个只包括 '\('，'\)'，'{'，'}'，'\['，'\]' 的字符串，判断字符串是否有效。

有效字符串需满足：

* 左括号必须用相同类型的右括号闭合。 
* 左括号必须以正确的顺序闭合。 

注意空字符串可被认为是有效字符串。

```cpp
bool isValid(string s) {
    stack<char> cstk;
    for(char ch : s){
        if(ch == '(' || ch == '[' || ch == '{')
            cstk.push(ch);
        else{
            if(cstk.empty())
                return false;
            if(ch == ')' && cstk.top() != '(')
                return false;
            if(ch == '}' && cstk.top() != '{')
                return false;
            if(ch == ']' && cstk.top() != '[')
                return false;
            cstk.pop();
        }
    }
    return cstk.empty();
}
```

### **有效的括号字符串【**[**链接**](https://leetcode-cn.com/problems/valid-parenthesis-string/)**】（百度面试）**

给定一个只包含三种字符的字符串：（ ，） 和 \*，写一个函数来检验这个字符串是否为有效字符串。有效字符串具有如下规则：

* 任何左括号 \( 必须有相应的右括号 \)。 
* 任何右括号 \) 必须有相应的左括号 \( 。 
* 左括号 \( 必须在对应的右括号之前 \)。
* 可以被视为单个右括号 \) ，或单个左括号 \( ，或一个空字符串。
* 一个空字符串也被视为有效字符串。

```cpp
bool checkValidString(string s) {
    stack<char> left_stk;
    stack<char> star_stk;
    for(int i = 0; i < s.size(); i++){
        if(s[i] == '(')
            left_stk.push(i);
        else if(s[i] == '*')
            star_stk.push(i);
        else{
            if(left_stk.empty() && star_stk.empty())
                return false;
            else if(!left_stk.empty())
                left_stk.pop();
            else
                star_stk.pop();
        }
    }
    while(!left_stk.empty() && !star_stk.empty()){
        if(left_stk.top() > star_stk.top())
            return false;
        left_stk.pop();
        star_stk.pop();
    }
    return left_stk.empty();
}
```

### 最长有效括号【[链接](https://leetcode-cn.com/problems/longest-valid-parentheses/)】



### **使括号有效的最少添加**【[链接](https://leetcode-cn.com/problems/minimum-add-to-make-parentheses-valid/)】

### 

### 删除无效的括号【[链接](https://leetcode-cn.com/problems/remove-invalid-parentheses/)】

### 

### 括号生成【[链接](https://leetcode-cn.com/problems/generate-parentheses/)】



