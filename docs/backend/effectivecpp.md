### cheat sheet
- `isalpha` 大小写英文字母
- `isdight` 0-9
- `isalnum` 上边两个
- `isblank` `\t` 和空格
- `stoi` 字符串变数字
- `to_string` 数字变字符串
- `1ll` 参与 `int` 计算时将结果转为 `long long` `1ll+anw`
- `floor(double x)` 向下取整
- `ceil(double x)` 向上取整
- `memeset` 只用来赋 0 或者 -1
- `string x.replace(int pos,int n,string new_str)` 效果为：从下标为 pos 的位置开始数 n 个字符（pos 也算一个），把他们删除，再插入 new_str ，可以配合 `x.find()` 使用
- vector删除特定元素 `nums.erase(remove(nums.begin(), nums.end(), val) ,nums.end());`

`stringstream` 用法
- 最好不要混着用输入输出，每次用的时候只用一个方向，要是换方向需要 `ss.str(string()); ss.clear()` 相当于用一个空字符串取代原来内容
- 用例：去除字符串的空格，用 `>>` 的好处是可以忽略所有空格，用 `getline` 的好处是可以指定分隔符，但是多个分隔符判断为空串,和 Python 效果一样，都没法分辨的，`['', 'aa', 'aaa', '', '', '', 'aa', 'aaa', '', '', 'a', 'aa', '', '', '']`
- 省流：如果字符串比较好，没有前导和尾空格，中间只有一个空格，用 `getline` 和 `>>` 都行，不然用 `>>` 效果好

```cpp
void learn() {
    string input = " aa aaa    aa aaa   a aa   ";
    string cache;

    stringstream ss(input);

    vector<string> res;
    while (ss >> cache) {
        res.emplace_back(cache);
    }
    for (string a: res) {
        cout << a << '+';
    }
    cout << endl;

    // Clear the stringstream before using it for output
    ss.str(string()); // Clear the contents
    ss.clear(); // Reset the stream state

    ss << input;
    vector<string> other;

    while (getline(ss, cache, ' ')) {
        other.emplace_back(cache);
    }
    for (string a: other)
        cout << a << '+';
}

//aa+aaa+aa+aaa+a+aa+        
//+aa+aaa++++aa+aaa+++a+aa+++

```

### pointer vs reference
pointer：

- 不同时间指向不同的物体
- 有可能指向null

reference：

- 不会为空（一定指向某个物体），不用检查null，所以创建的时候就初始化（赋值）

const pointer

从右往左读，遇到 `*` 替换成 a point to 

- `const int * p`: p is a pointer to int const
- `int const * p`: p is a pointer to const int

一句话概括： `*` 前修饰的被指向对象， `*` 后是修饰指针。[来源](https://pengfeixc.com/blogs/clang/const-pointer-and-pointer-to-const)

```cpp
char p1[] = "hello";
char* npnd = p1;             // non-const pointer,non-const data
const char* npcd = p1;       // non-const pointer,const data
char* const cpnd = p1;       // const pointer,non-const data
const char* const cpcd = p1; // const pointer,const data
```

### 类型转换
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

### `overload` and `override`

- `overload` in same range, same function name,different args
- `override` in different range(base and derived class), same function name, same args, but the function is **virtual**
- `overwrite` in different range, same function name, same args, but the funciton is **not** virtual, function will hide the same in base class. 函数 'writeFunc' 从 类 'father' 中隐藏了一个非虚拟函数

[参考](https://wuciawe.github.io/object%20oriented/2014/08/29/override-overload-overwrite.html)

```cpp
#include <iostream>

using namespace std;

class father {
public:
    virtual void func(int a) {
        cout << "in father virtual func\n";
    }

    void writeFunc(int x) {
        cout << "in father overwrite func\n";
    }
};

class son : public father {
public:
    //override
    void func(int a) override {
        cout << "in son override func\n";
    }

    void getMax(int a, int b) {
        cout << max(a, b) << endl;
    }

    // overload
    void getMax(int a, int b, int c) {
        cout << max(a, max(b, c)) << endl;
    }

    //overwrite
    void writeFunc(int x) {
        cout << "in son wirte func\n";
    }
};

void test() {
    son s1;
    s1.func(1);
    s1.writeFunc(3);
    s1.getMax(1, 2);
    s1.getMax(3, 4, 5);
}

int main() {
    test();
    return 0;
}
```

## C++ Q&A
[异或交换变量不好](https://blog.csdn.net/solstice/article/details/5166912)

为什么 C++ 没有 `interface` ? 

有一种[说法](https://stackoverflow.com/questions/478725/why-is-the-oo-concept-interface-not-represented-by-a-keyword-in-c)是 C++ 支持多重继承，而 Java 不支持，所以就没设计关键字 

---

`unorder_map.earse()`，我们知道有一种移除方式是根据键移除，很自然想到这样的情况
```cpp
unordered_map<int, int> map;
for (int i = 1; i <= 3; i++)
    map[i] = 1;
for (auto x : map) {
    map[x.first]--;
    if (map[x.second] == 0) {
        cout << "erase " << x.first << endl;
        map.erase(x.first);
    }
}
```
然后就死循环了，非常奇怪，但是不在循环里，一个个的移除就没问题。

---

`\n` 不会强制刷新，速度快；`endl` 强制刷新，速度慢