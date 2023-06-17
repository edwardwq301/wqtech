# Java
## 安装
vscode的话直接把jdk安装（一直下一步就没出错，装到指定的文件夹就总有问题😅），还是直接上ide比较方便
## 感想
Java比C++更加繁琐，写起来麻烦，相对的更加面向对象
还不是很清楚Java的引用和C++的指针，引用的区别
## note
- 变量都是对象的引用
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
- `static`是类共享的，和C++一样
- 更改器方法，访问器方法
- 每个类都可以有一个静态main来测试
- 好像Java中没有函数参数缺省的功能