# Chapter 1:Introduction
## 1. The First C++ Program
```cpp
#include<iostream>
usingnamespace std;
int main()
{  
    int age;
    int sid;
    cin >> age >>sid;
    cout << "Hello, World! I am " << age << Today!" << endl;  
    return 0;
}
```
- `cout`: 标准输出流
- `<<`:把东西插入到左边去
- `cout<<""`的副作用是字符串被输出，但结果是字符串本身。
- `cin>>age`同理，副作用是读入，结果是 age 本身。***(读到空格为止)***
> 语句本身是有结果的
## 2. String
- string is a class in C++. (需要 #include `<string>`)
- 可以像定义其他类型一样定义变量。 e.g. string str;
- 可以对字符串初始化，用 cin, cout 输入输出。
### 2.1 Assignment for string
```cpp
char charr1[20];
char charr2[20] = "jaguar"; 
string str1;
string str2 = "panther"; 
carr1 = char2; // illegal 
str1 = str2; // legal
```

字符数组不能赋值，字符串是可以的。
这里 "panther" 是一个字符串字面量。

### 2.2 字符串的连接
```cpp
string str3;
str3 = str1 + str2;
str1 += str2;
str1 += "lalala";
```

> `string name; name+="Johnson";`这里`name`已经有确定值了，因为这里是一个class态，为空字符串 
### 2.3 字符串的长度
`s.length()`得到字符串的长度。(***C++中字符串没有`\0`***)
- C语言中`.`用来检索结构里的成员
- C++中`.`的做法是在结构里放入了函数，成了类
### 2.4 Create a string 初始化字符串
```cpp
string s;//s室=是被初始化的
int i;//i是没有被初始化的
```

`string major("CS");`这样也可以初始化一个字符串。类似地，其他类型也可以 `int age(18)`
```cpp
s.string(const char *cp,int len);
s.string(const string& s2,int pos);
s.string(const string& s2,int pos,int len);
```
### 2.5 其他成员函数
- sub-string：`substr(int pos,int len);`拷贝字符从`pos`位置开始的len个字符
    - 如果`pos` 超出字符串长度，那么会产生异常
    - 如果`pos`等于字符串长度，那么会得到空字符串
    - 如果`pos+len`超出了字符串的长度，那么只会拷贝到字符串的末尾。
- alter string
    - assign将一个新的值赋值给字符串
    ```cpp
    s.assign(const string& str);//将s赋值为str中的值
    s.assign(const string& str, size_t subpos, size_t sublen);//赋值为sstr的一个子串
    s.assign(const char* s);
    s.assign(const char* s, size_t n);
    s.assign(size_t n, char c);
    ```
    - `insert`在`pos`之间插入字符
    ```cpp
    s.insert(size_t pos,const string& str);
    s.insert(size_t pos,const string& str,size_t subpos,size_t sublen);
    s.insert(size_t pos,const char* s);
    s.insert(size_t pos,const char* s,size_t n);
    s.insert(size_t pos,size_t n,char c);
    ```
    - `find(const string& str,size_t pos=0)`从pos开始查找字符串str，返回第一次匹配的第一个字符串的位置
    - `erase`
    ```cpp
    s.erase(size_t pos=0,size_t len=npos);
    ```
    - `append`在字符串后面添加字符串
    ```cpp
    s.append(const string& str);
    ```
    - `replace`
    ```cpp
    s.replace(size_t pos,size_t len,const string& str);
    ```
### 2.6 Pointers to Object
```cpp
string s="hello";
string *ps=&s;//s指向ps的地址，也就是该字符串大家一个字符的地址
```

### 2.7 Two ways to Access
- `string s;`s就是对象本身
    - 在这个语句中，对象s已经被创建和初始化(一个空字符串)
- `string *ps;`ps指向对象的指针
    - 在这个语句中，ps指向的对象是未知的