## pointer vs reference
pointer：

- 不同时间指向不同的物体
- 有可能指向null

reference：

- 不会为空（一定指向某个物体），不用检查null，所以创建的时候就初始化（赋值）

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

## 不建议重载类中的运算符
坏处多于好处