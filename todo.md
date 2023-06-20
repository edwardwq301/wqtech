



//
////演示动物类的定义，其中midnight方法的输入参数为Behavior类型
//class Animal {
//    private String name; // 动物名称
//
//    public Animal(String name) {
//        this.name = name;
//    }
//
//    public String getName() {
//        return this.name;
//    }
//
//    // 定义一个半夜行动的方法。具体的动作由输入行为的act方法执行
//    public void midnight(Behavior behavior) {
//        behavior.act();
//    }
//
//}
//
//
////定义一个行为接口，给动物类调用
//interface Behavior {
//    public void act(); // 声明一个名叫行动的抽象方法
//}
//
//
////演示外部如何调用函数式接口
//public class main {
//
//    public static void main(String[] args) {
//        testCock(); // 测试公鸡在半夜干了啥
//        testCat(); // 测试老猫在半夜干了啥
//        testPig(); // 测试猪仔在半夜干了啥
//    }
//
//    // 测试公鸡在半夜干了啥
//    private static void testCock() {
//        Animal cock = new Animal("公鸡"); // 创建一个公鸡实例
//        // 调用midnight方法时，传入匿名内部类的实例
//        cock.midnight(new Behavior() {
//            @Override
//            public void act() {
//                System.out.println(cock.getName() + "在叫啦。");
//            }
//        });
//        // 调用midnight方法时，传入Lambda表达式的代码。
//        // 匿名方法不存在输入参数的话，也要保留一对圆括号占位子。
//        cock.midnight(() -> System.out.println(cock.getName() + "在叫啦。"));
//    }
//
//    // 测试老猫在半夜干了啥
//    private static void testCat() {
//        Animal cat = new Animal("老猫"); // 创建一个老猫实例
//        // 调用midnight方法时，传入Lambda表达式的代码
//        cat.midnight(() -> System.out.println(cat.getName() + "在捉老鼠。"));
//    }
//
//    // 测试猪仔在半夜干了啥
//    private static void testPig() {
//        Animal pig = new Animal("猪仔"); // 创建一个猪仔实例
//        // 调用midnight方法时，传入Lambda表达式的代码
//        pig.midnight(() -> System.out.println(pig.getName() + "在呼呼大睡。"));
//    }
//
//}