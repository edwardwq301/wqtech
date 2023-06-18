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
- 子类继承构造函数时只继承了默认构造函数（无参）
- final定义的类无法再派生，final定义的函数不能重写
- Java不支持多重继承，那我想达到这个效果该怎么办，就引出接口的用途。接口可以看成和类平级。

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



## Q&A
[==null和.equal(null)的区别](https://stackoverflow.com/questions/4501061/java-null-check-why-use-instead-of-equals)
简单来说，`==null`看的是不是同一个引用，`.equal()`是调用方法，如果它本身是null，那就会引发NullPointerException。**看是不是null，直接用`==null`或者`Objects.equals(onePossibleNull, twoPossibleNull)`就行了**



