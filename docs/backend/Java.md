---
hide:
    - toc
---

# Java
## 安装
vscode的话直接把jdk安装（一直下一步就没出错，装到指定的文件夹就总有问题😅），还是直接上ide比较方便

## 感想
Java比C++更加繁琐，写起来麻烦，相对的更加面向对象
还不是很清楚Java的引用和C++的指针，引用的区别

## note
### part1
- 变量都是对象的引用
- 只有基本类型不是对象
- 所有参数都是值传递
- 一个方法不能修改基本数据类型的参数
- 一个方法可以改变对象参数的状态
- 不能让对象参数引用一个新的对象（做不到swap）
??? note "例子"
    ```java
      public class Main {
    
        public static void main(String[] args) {
            employee ashen = new employee(100);
            testPara(ashen);
            /*
            传的是ashen的复制，这个复制也引用了那个对象
            所以能修改salary
            */
            System.out.println("para x");
            int testX = 10;
            minus2(testX);
            /*
            传的是testX值的复制，最后不会修改testX
             */
            System.out.println(testX);
        }
    
        public static void testPara(employee e1) {
            e1.showSalary();
            e1.raiseSalary(100);
            e1.showSalary();
        }
    
        public static void minus2(int x) {
            System.out.println("para is " + x);
            x = x - 2;
            System.out.println("para changed is " + x);
        }
    }
    
    class employee {
    
        private int salary;
    
        public employee(int salary) {
            this.salary = salary;
        }
    
        public void raiseSalary(int adding) {
            salary += adding;
        }
    
        public void showSalary() {
            System.out.println(salary);
        }
    }
    ```

### part2
- `static`是类共享的，和C++一样
- 更改器方法，访问器方法
- 每个类都可以有一个静态main来测试
- 好像Java中没有函数参数缺省的功能
- 包装不能通过强制类型转换成基本类型，需要用intValue
    - 比较用equals,不用==
- 子类继承构造函数时只继承了默认构造函数（无参），但是可以`super(x,y)`调用父类的有参构造函数
- final定义的类无法再派生，final定义的函数不能重写
- Java不支持多重继承，那我想达到这个效果该怎么办，就引出接口的用途。接口可以看成和类平级。
- 接口不是类，但是可以声明接口类型的变量，并用实现了接口的类去初始化接口变量


|Java8开始的接口||
|:--||
前缀default方法|可继承，不可重写
static属性（变量）|默认是终态，final可加可不加
static方法可以重写|不能被继承,接口的静态方法和实现类的静态方法没有任何联系，只是恰好名字一样|
|**修饰符**||
private|仅对本类可见
public|所有类可见
protected|本包和所有类可见
默认|本包

??? example "interface example"
    ```java
    import java.util.Random;

    public class Main {
        public static void main(String[] args) {
            eagle e1 = new eagle("peter");
            eagle e2 = new eagle("ash");
            System.out.println("" + e1.getName() + " " + e1.getTotalCount());
            System.out.println("" + e2.getName() + " " + e2.getTotalCount());
            e1.flying();
            //此处的getHeight是eagle自己的，
            // 不是接口的，只是名字恰好一样
            e1.getHeight();
            //只能从接口访问
            fly.getHeight();
        }
    }

    interface fly {
        default public void flying() {
            System.out.println("flying");
        }

        static void getHeight() {
            Random random = new Random();
            int height = random.nextInt(100);
            System.out.println(height);
        }
    }

    class eagle implements fly {
        static int totalCount;
        String name;

        eagle(String name) {
            this.name = name;
            totalCount++;
        }

        public void getHeight() {
            Random random = new Random();
            int height = random.nextInt(100);
            System.out.println(height);
        }

        public String getName() {
            return name;
        }

        public int getTotalCount() {
            return totalCount;
        }
    }
    ```

### part3
- 函数式接口:一个接口有且仅有一个抽象方法
(源地址)[https://github.com/aqi00/java/blob/master/chapter08/src/com/special/function/TestFunctional.java]
??? example
    ```java
    //演示动物类的定义，其中midnight方法的输入参数为Behavior类型
    class Animal {
        private String name; // 动物名称

        public Animal(String name) {
            this.name = name;
        }

        public String getName() {
            return this.name;
        }

        // 定义一个半夜行动的方法。具体的动作由输入行为的act方法执行
        public void midnight(Behavior behavior) {
            behavior.act();
        }

    }


    //定义一个行为接口，给动物类调用
    interface Behavior {
        public void act(); // 声明一个名叫行动的抽象方法
    }


    //演示外部如何调用函数式接口
    public class main {

        public static void main(String[] args) {
            testCock(); // 测试公鸡在半夜干了啥
            testCat(); // 测试老猫在半夜干了啥
            testPig(); // 测试猪仔在半夜干了啥
        }

        // 测试公鸡在半夜干了啥
        private static void testCock() {
            Animal cock = new Animal("公鸡"); // 创建一个公鸡实例
            // 调用midnight方法时，传入匿名内部类的实例
            cock.midnight(new Behavior() {
                @Override
                public void act() {
                    System.out.println(cock.getName() + "在叫啦。");
                }
            });
            // 调用midnight方法时，传入Lambda表达式的代码。
            // 匿名方法不存在输入参数的话，也要保留一对圆括号占位子。
            cock.midnight(() -> System.out.println(cock.getName() + "在叫啦。"));
        }

        // 测试老猫在半夜干了啥
        private static void testCat() {
            Animal cat = new Animal("老猫"); // 创建一个老猫实例
            // 调用midnight方法时，传入Lambda表达式的代码
            cat.midnight(() -> System.out.println(cat.getName() + "在捉老鼠。"));
        }

        // 测试猪仔在半夜干了啥
        private static void testPig() {
            Animal pig = new Animal("猪仔"); // 创建一个猪仔实例
            // 调用midnight方法时，传入Lambda表达式的代码
            pig.midnight(() -> System.out.println(pig.getName() + "在呼呼大睡。"));
        }

    }
    ```

- Java8开始，每种容器都支持`forEach`和lambda表达式联合使用
??? exemple
    ```java
    import java.util.HashMap;
    import java.util.HashSet;

    public class Employee {
        public static void main(String[] args) {
            HashMap<String, Employee> map = new HashMap<String, Employee>();
            map.put("boss", new Employee("wq"));
            map.put("clerk", new Employee("jyy"));

            map.forEach((String key, Employee value) -> System.out.printf("%s,%s\n", key, value.getName()));
            
        }

        public String getName() {
            return name;
        }

        String name;

        Employee(String name) {
            this.name = name;
        }
    }
    ```

### part4
- 异常try catch finally(无论是执行了try还是catch，都要执行finally的语句)
- 通过反射操作对象的私有属性或者私有方法。先获取Class,再获取Field，setAccessible,setInt。
- 

### TODO

- [ ] `Comparator`的键提取器函数 v10page242
- [ ] 泛型接口（断言接口，消费接口，函数接口）

## Q&A

### [==null和.equal(null)的区别](https://stackoverflow.com/questions/4501061/java-null-check-why-use-instead-of-equals)
简单来说，`==null`看的是不是同一个引用，`.equal()`是调用方法，如果它本身是null，那就会引发NullPointerException。**看是不是null，直接用`==null`或者`Objects.equals(onePossibleNull, twoPossibleNull)`就行了**

### 自定义比较
1. 在类中应用`Comparable<someClass>`接口，重写`public int compareTo(someClass s2)`，来实现类自带比较
2. 在需要`Comparator`的地方写一个匿名的类或者lamada表达式
!!! example "自定义比较"
    ```java
    import java.util.ArrayList;
    import java.util.Comparator;

    public class main {
        public static void main(String[] args) {
            ArrayList<Employee> arrayList = new ArrayList<Employee>();
            for (int i = 0; i < 4; i++) {
                String name = "ename";
                for (int j = 0; j < i; j++)
                    name = name + j;
                Employee employee = new Employee(i, name);
                arrayList.add(employee);
            }
            //通过接口实现方法自带
            arrayList.sort(Employee::compareTo);
            //new一个Comparator匿名内部类
            arrayList.sort(new Comparator<Employee>() {
                @Override
                public int compare(Employee o1, Employee o2) {
                    if (o1.getName().length() > o2.getName().length())
                        return -1;
                    else if (o1.getName().length() < o2.getName().length()) {
                        return 1;
                    } else
                        return 0;
                }
            });

            //lamada表达式
            arrayList.sort(((o1, o2) -> o1.name.length()>o2.name.length()?-1:1));
      
            //lamada表达式
            arrayList.sort((o1, o2) -> {
                if (o1.getName().length() > o2.getName().length())
                    return -1;
                else if (o1.getName().length() < o2.getName().length()) {
                    return 1;
                } else
                    return 0;
            });
        }


    }

    class Employee implements Comparable<Employee> {
        public int salary;
        String name;

        @Override
        public int compareTo(Employee e2) {
            if (this.salary > e2.salary) return -1;
            else if (this.salary < e2.salary) return 1;
            else return 0;
        }

        public Employee(int sal, String name) {
            salary = sal;
            this.name = name;
        }

        public String getName() {
            return name;
        }

        public int getSalary() {
            return salary;
        }
    }
    ```

### 求两个日期间隔天数

```java
LocalDate start=LocalDate.now();
LocalDate end = LocalDate.of(2025,5,5);
Duration between = Duration.between(start.atStartOfDay(), end.atStartOfDay());
System.out.println(between.toDays());
```

### javafx展示图片

[传送门](https://www.tutorialspoint.com/how-to-display-an-image-in-javafx)

### Java scanner关闭
如果scanner的sys.in实例关闭了，即`实例名.close()`，不能再创建sys.in实例

[StackOverflow](https://stackoverflow.com/questions/58244954/why-cant-i-just-create-another-scanner-object-after-using-scanner-close)
[csdn相关](https://blog.csdn.net/Monkey_Long_/article/details/105409842)

### 数字和字符串混合输入
今天遇到这样一个情况，先输入数字，再输入字符串的时候总是空串。Java这个性质也太坑了😡
相关解决办法[传送门](https://blog.csdn.net/yuekangwei/article/details/108568494)