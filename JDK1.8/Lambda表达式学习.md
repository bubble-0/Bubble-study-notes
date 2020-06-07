# Lambda表达式学习

## 简介
Java 8的Lambda表达式借鉴了C#和Scala等语言中的类似特性，简化了匿名函数的表达方式。Lambda表达式可以直接以内联的形式为函数式接口的抽象方法提供实现，并把整个表达式作为函数式接口的实例。什么是函数式接口？简单来说就是只包含一个抽象方法的接口，允许有默认的实现（使用default关键字描述方法）。函数式接口建议使用@FunctionalInterface注解标注，虽然这不是必须的，但是这样做更符合规范。
使用 Lambda表达式可以使代码变的更加简洁紧凑。

## 语法
### 语法格式如下
```
(parameters) -> expression
或
(parameters) ->{ statements; }
```
- 参数列表；
- 箭头->把参数列表与Lambda主体分隔开；
- Lambda主体，只有一行代码的时候可以省略大括号和return关键字。

### 主要特征
- 可选类型声明：不需要声明参数类型，编译器可以统一识别参数值。
- 可选的参数圆括号：一个参数无需定义圆括号，但多个参数需要定义圆括号。
- 可选的大括号：如果主体包含了一个语句，就不需要使用大括号。
- 可选的返回关键字：如果主体只有一个表达式返回值则编译器会自动返回值，大括号需要指定明表达式返回了一个数值。

### 案例
```
// 1. 不需要参数,返回值为 5  
() -> 5  
  
// 2. 接收一个参数(数字类型),返回其2倍的值  
x -> 2 * x  
  
// 3. 接受2个参数(数字),并返回他们的差值  
(x, y) -> x – y  
  
// 4. 接收2个int型整数,返回他们的和  
(int x, int y) -> x + y  
  
// 5. 接受一个 string 对象,并在控制台打印,不返回任何值(看起来像是返回void)  
(String s) -> System.out.print(s)
```

## 用法示例
### 1.多个参数
``` java
    /**
    * eg:集合排序
    */

    // 准备一个集合
    List<Integer> list = Arrays.asList(10, 5, 25, -15, 20);

    //jdk1.7写法
    Collections.sort(list, new Comparator<Integer>() {
    @Override
    public int compare(Integer o1, Integer o2) {
        return o1 - o2;
    }
    });
    System.out.println(list);

    List<Integer> list1 = Arrays.asList(10, 5, 25, -15, 20);

    //jdk1.8写法
    //list1.sort((i1,i2) -> {return i1 - i2;});
    //简写
    list1.sort((i1, i2) -> i1 - i2);

    System.out.println(list1);

```

### 2.单个参数
``` java
    /**
    * eg:打印集合内容
    */

    //jdk1.7
    for (Integer i : list) {
        System.out.print(i + " , ");
    }

    System.out.println();

    //jdk1.8
    list.forEach((i) -> {
        System.out.print(i + " , ");
    });
    System.out.println();
    //简写
    list.forEach(i -> System.out.print(i + " , "));
    System.out.println();

```

### 3.把Lambda赋值给变量
``` java
    /**
    * eg:赋值
    * 把lambda表达式结果赋值给变量
    * 我们可以通过lambda表达式来实例化接口
    * 不过该用法很少见一般都是用来做参数
    */
    Runnable runnable = () -> {
        //直接使用匿名内部类编写run方法
        System.out.println("lambda  new runnable");
    };

    new Thread(runnable).start();

    //如果不用lambda表达式
    new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("jdk 1.7 new runnable");
        }
    }).start();

```

### 4.隐式final
``` java 
    /**
    * eg:隐式final
    * Lambda表达式的实质其实还是匿名内部类，
    * 而匿名内部类在访问外部局部变量时，
    * 要求变量必须声明为final！
    * 不过我们在使用Lambda表达式时无需声明final，
    * 这并不是说违反了匿名内部类的规则，
    * 因为Lambda底层会隐式的把变量设置为final，
    * 在后续的操作中，一定不能修改该变量
    *
    * 为什么匿名内部类中要求变量必须是final的?
    * 内部类会自动拷贝外部变量的引用，
    * 为了避免：1. 外部方法修改引用，而导致内部类得到的引用值不一致
    * 2.内部类修改引用，而导致外部方法的参数值在修改前和修改后不一致。
    * 于是就用 final 来让该引用不可改变
    */
    int i = 0;

    //正确方式
    Runnable r = () -> {
        System.out.println("正确方式调用 :" + i);
    };
    new Thread(r).start();

    //错误方式,编译就会报错
    /* Runnable r2 = () -> {
        System.out.println("错误方式调用 :" + i ++);
    };
    new Thread(r2).start();*/

```

## 函数式接口
### 简介
简单总结：
- Lambda表达式是接口的匿名内部类的简写形式
- 接口必须满足：内部只有一个函数
函数式接口在java中是指:有且仅有一个抽象方法的接口，这样的接口，我们称为函数式接口，我们学过的`Runnable`、`Comparator`都是函数式接口的典型代表。但是在实践中，函数接口是非常脆弱的，只要有人在接口里添加多一个方法，那么这个接口就不是函数接口了，就会导致编译失败。Java 8提供了一个特殊的注解`@FunctionalInterface`来克服上面提到的脆弱性并且显示地表明函数接口。而且jdk8版本中，对很多已经存在的接口都添加了`@FunctionalInterface`注解,一旦使用该注解来定义接口，编译器将会强制检查该接口是否确实有且仅有一个抽象方法，否则将会报错。需要注意的是，即使不使用该注解，只要满足函数式接口的定义，这仍然是一个函数式接口，使用起来都一样。(该接口是一个标记接口)

备注：“语法糖"是指使用更加方便，但是原理不变的代码语法。例如在遍历集合时使用的for-each语法，其实底层的实现原理仍然是迭代器，这便是“语法糖”。从应用层面来讲，Java中的Lambda可以被当做是匿名内部类的“语法糖”，但是二者在原理上是不同的。

### 调用函数式接口
``` java

/**
 * 自定义函数式接口
 */
@FunctionalInterface
public interface MyFuncationInterface {

    //自己定义一个抽象方法
    void myFuncationMethod();

}

/**
 * java 8 函数式接口调用
 */
public class FunctionInterfaceDemo {

    //将函数式接口当做参数
    public static void excuteMyFunctionMethod(MyFuncationInterface myFuncationInterface) {
        myFuncationInterface.myFuncationMethod();
    }

    public static void main(String[] args) {
        /**
         * 调用函数式接口,和之前匿名内部类调用的方式同理
         */
        excuteMyFunctionMethod(() ->
            System.out.println("执行MyFuncationMethod")
        );
        
    }
}

```

### 函数式编程-lambda的延迟执行
有些场景的代码执行后，结果不一定会被使用，从而造成性能浪费。而Lambda表达式是延迟执行的，这正好可以作为解决方案，提升性能。
``` java
/**
 * lambda 表达式的懒加载案例
 */
public class LazzyLoadingDemo {
    //只有在级别1的情况下才会打印日志
    public static void log(int level, String mes) {
        if (level == 1) {
            System.out.println(mes);
        }
    }

    public static void lambdalog(int level, MessageBuild messageBuild) {
        if (level == 1) {
            System.out.println(messageBuild.buildMessage()); //利用内部类懒加载的原理此处先不执行，把计算方式在调用时利用匿名内部类的方式执行
        }
    }

    public static void main(String[] args) {
        String msgA = "HELLO ";
        String msgB = "LAMBDA";
        String msgC = "FUNCATIONINTERFACE";

        //级别1的条件是不一定达到的，但是后面的字符串还是会处理
        log(1, msgA + msgB + msgC);

        lambdalog(1, () ->{
            System.out.println("lambda is excute 1");
            return msgA + msgB + msgC;}
        );

        lambdalog(2, () ->{
            System.out.println("lambda is excute 2");
            return msgA + msgB + msgC;}
        );

    }
}

/**
 * lambda函数式接口优雅写法
 */
@FunctionalInterface
interface MessageBuild {

    String buildMessage();
}
```

## 1.常用Function类型接口
### 1.Supplier接口
``` java
 public class SupplierExample {
    SupplierExample(){
        System.out.println("调用SupplierExample构造方法");
    }
    
    private static String test_Supplier(Supplier<String> suply) {
        return suply.get();
    }

    private static Integer getArrMax(Supplier<Integer> maxInt) {
        return maxInt.get();
    }

    public static void main(String[] args) {
        /**
         *  Supplier接口(供应接口)
         *  java.util.function.Supplier<T> 接口仅包含一个无参的方法： T get() 。
         *  用来获取一个泛型参数指定类型的对象数据。由于这是一个函数式接口
         *  这也就意味着对应的Lambda表达式需要“对外提供”一个符合泛型类型的对象数据。
         *  需要调用get方法才能获取定义的值
         */
        System.out.println(test_Supplier(() ->
            "test_Supplier 返回的字符串"
        ));

        //一个小总结，只要类型确定了就可以使用lambda表达式来写
        Supplier<String> str = () -> "lambda返回的字符串";
        System.out.println(str.get());

        //或者这样，只要满足函数式表达式的情况就可以
        System.out.println(((Supplier<String>) () -> "匿名内部类返回的字符串").get());


        /**
         * Supplier容器使用
         * 创建Supplier容器，每次调用get方法都会重新实例化一个对象，调用对象的构造方法
         */

        //创建容器，此时并不会实例化对象
        Supplier<SupplierExample> supplierExamples = SupplierExample::new;

        //调用构造方法
        SupplierExample supplierExample1 = supplierExamples.get();

        //调用构造方法
        SupplierExample supplierExample2 = supplierExamples.get();

        //结果并不相等
        System.out.println(supplierExample1 == supplierExample2);


        /**
         * 练习：求数组元素最大值
         */
        int arr [] = {13,32,35,47,5,62,7,28,101,11};

        System.out.println("数据元素最大值" +
        getArrMax(() -> {
            Arrays.sort(arr);
            return arr[arr.length - 1 ];
        }));

    }
}



```

