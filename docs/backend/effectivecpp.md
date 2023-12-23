## pointer vs reference
pointer：

- 不同时间指向不同的物体
- 有可能指向null

reference：

- 不会为空（一定指向某个物体），不用检查null，所以创建的时候就初始化（赋值）

### const pointer

- `const`在`*`左边，所指物为`const` （指针可以不同时间指向不同物体，但是物体内容不能改变）
- `const`在`*`右边，指针为`const` （指针一直指向一个物体，但是物体内容可改变）


```cpp
char p1[] = "hello";
char* npnd = p1;             // non-const pointer,non-const data
const char* npcd = p1;       // non-const pointer,const data
char* const cpnd = p1;       // const pointer,non-const data
const char* const cpcd = p1; // const pointer,const data
```

## 类型转换
`const_cast< >` 把const转为非const，去掉constness或者volatileness

```cpp
int x = 10;
const int& cx = x;
const_cast<int&>(cx) = 2;
```

`dynamic_cast< >` 安全地把父类指针或引用转换成子类指针或引用

- 指针转换失败：传一个null指针
- 引用转换失败：抛出异常`std::bad_cast`

`static_cast< >` 类型转换[功能多：](https://en.cppreference.com/w/cpp/language/static_cast)

```cpp
#include <iostream>
using namespace std;

enum class num {
    first = 11,
    second,
    third
};

int main() {
    num e = num::third;
    // enum to int
    int x = static_cast<int>(e);
    cout << x;
    return 0;
}
```

## class 
### 使用前先初始化

- 推荐使用初始化列表进行初始化
- 不在构造函数内进行赋值（来提升效率）
- 初始化列表的顺序和声明顺序相同（编译器给先声明的进行初始化）

### 不想要自动生成的函数
假如不想要编译器自动产生的**copy构造函数和copy assignment操作符**可以这么做：

- 将函数声明为`private`并且不实现
- 或者将他的父类的函数声明为`private`并使用`private`继承

| 基类成员  | 公有派生  | 私有派生  | 保护派生  |
| :-------: | :-------: | :-------: | :-------: |
|  private  | unvisited | unvisited | unvisited |
| protected | protected |  private  | protected |
|  public   |  public   |  private  | protected |


## C++ Q&A
- 为什么 C++ 没有 `interface`
    - 有一种[说法](https://stackoverflow.com/questions/478725/why-is-the-oo-concept-interface-not-represented-by-a-keyword-in-c)是 C++ 支持多重继承，而 Java 不支持，所以就没设计关键字 
