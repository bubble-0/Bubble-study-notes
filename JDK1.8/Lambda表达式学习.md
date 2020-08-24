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

### 2.Consumer接口
``` java
/**
 * java.util.function.Consumer<T> 接口则正好与Supplier接口相反，
 * 它不是生产一个数据，而是消费一个数据，其数据类型由泛型决定
 * <p>
 * 提供了一个默认方法andThen
 * 如果一个方法的参数和返回值全都是 Consumer 类型，
 * 那么就可以实现效果：消费数据的时候，首先做一个操作，然后再做一个操作，实现组合
 * <p>
 * default Consumer<T> andThen(Consumer<? super T> after) {
 * Objects.requireNonNull(after);
 * return (T t) ‐> { accept(t); after.accept(t); };
 * //1:  返回值为Consumer 那么需要 ()-> 表示函数式接口
 * //2:  accept(t);为生产一个数据供应给 (T t)中的t
 * //3:  after.accept(t);为利用这个t再次生成新的函数式接口 实现类始于builder的设计模式
 * }
 * <p>
 * 执行顺序，先用lambda实现逻辑之后，执行accept函数执行lambda方法，即重写的accept方法
 */
public class ConsumerExample {
    public static void generateX(Consumer<String> consumer) {
        consumer.accept("hello consumer");
        consumer.accept("secon consumer");
        consumer.accept("hi consumer");
    }

    public static void formatPersonMsg(Consumer<String[]> con1, Consumer<String[]> con2) {
        //组合作用。使用andThen方法，con1.andThen(con2).accept();会使两者都消费
        con1.andThen(con2).accept(new String[]{"迪丽热巴,女", "古力娜扎,女", "马尔扎哈,男"});
    }

    public static void main(String[] args) {
        generateX(s -> System.out.println(s));

        formatPersonMsg(s1 -> {
            for (String s : s1) {
                System.out.print(s.split("\\,")[0] + " ");
            }
            System.out.println();
        }, s2 -> {
            for (String s : s2) {
                System.out.print(s.split("\\,")[1] + " ");
            }
        });

        System.out.println();

        printInfo(r -> System.out.println(r.split("\\,")[0]),
                r -> System.out.println(r.split("\\,")[1]), new String[]{"小明,12", "小红,15", "小让,128"});

    }

    //自产自销
    private static void printInfo(Consumer<String> con1, Consumer<String> con2, String[] arr) {
        for (String s : arr) {
            con1.andThen(con2).accept(s);
        }
    }
}
```

### 3.PredicateExample接口
``` java
public class PredicateExample {
    private static void method_test(Predicate<String> predicate) {
        boolean b = predicate.test("ABC DEF");
        System.out.println(b);
    }

    private static void method_or(Predicate<String> predicate,Predicate<String> predicate2) {
        boolean b = predicate.or(predicate2).test("ABC DEF");
        System.out.println(b);
    }

    private static void method_and(Predicate<String> predicate,Predicate<String> predicate2) {
        boolean b = predicate.and(predicate2).test("ABC DEF");
        System.out.println(b);
    }

    private static void method_negate(Predicate<String> predicate) {
        boolean b = predicate.negate().test("ABC DEF");
        System.out.println(b);
    }



    public static void main(String[] args) {
        /**
         * 有时候我们需要对某种类型的数据进行判断，从而得到一个boolean值结果。
         * 这时可以使用java.util.function.Predicate<T> 接口
         *
         *  (s)->  函数式接口有参数 表示有有产生数据
         *
         *  (s)-> 具体的返回数据 看要是否原函数式接口给出了
         *
         * 抽象方法：test
         *
         * Predicate 接口中包含一个抽象方法： boolean test(T t) 。用于条件判断的场景：
         *
         * 默认方法：and or nagte (取反)
         *
         * 既然是条件判断，就会存在与、或、非三种常见的逻辑关系。其中将两个 Predicate 条件使用“与”逻辑连接起来实
         *
         * 现“并且”的效果时,类始于 Consumer接口 andThen()函数 其他三个雷同
         */

        method_test(i->i.contains("A"));

        method_or(i->i.contains("C"),i->i.contains("Z"));

        method_or(i->i.contains("X"),i->i.contains("Z"));

        method_and(i->i.contains("A"),i->i.contains("B"));

        method_and(i->i.contains("A"),i->i.contains("Z"));

        method_negate(i->i.contains("A"));
        method_negate(i->i.contains("Z"));

        //静态方法 isEqual 判断是否为同一个对象
        Predicate<String> predicate1 = i-> i.contains("A");
        Predicate<String> predicate3 = i-> i.contains("B");



        boolean b = Predicate.isEqual(predicate1).test(predicate1);
        System.out.println(b);

        System.out.println(Predicate.isEqual(predicate1).test(predicate3));


    }
}
```

### 4.Function 接口
``` java
public class FuncationExample {
    public static String method_apply(Function<Integer, String> function) {
        return function.apply(11);
    }

    //andThen 先计算前面的funcaion,在计算后面的
    private static Integer method_andThen(Function<Integer, Integer> function, Function<Integer, Integer> function2) {
        return function.andThen(function2).apply(11);
    }

    //compose 先计算后面的,在计算前面的
    private static Integer method_ccompose(Function<Integer, Integer> function, Function<Integer, Integer> function2) {
        return function.compose(function2).apply(11);
    }

    public static void main(String[] args) {
        /**
         * java.util.function.Function<T,R> 接口用来根据一个类型的数据得到另一个类型的数据，
         * 前者称为前置条件，后者称为后置条件 T是参数R是返回值
         */

        //直接使用
        System.out.println(method_apply(i -> i.toString()));

        /**
         * 默认方法 andThen  compose():
         * Function 接口中有一个默认的 andThen  compose方法，用来进行组合操作。
         * JDK源代码如：
         *  default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
         *      Objects.requireNonNull(after);
         *      return (T t) -> after.apply(apply(t));// 先执行调用者,再执行after的apply犯法
         *  }  // 这里的V 一个是作为输入值 一个是作为输出值  按照调用的顺序的不同 对于 T V 做输入 输出的顺序也不同 注意看
         *
         *  default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
         *      Objects.requireNonNull(before);
         *      return (V v) -> apply(before.apply(v));// 后执行before的apply方法,后执行调用者apply方法
         *  }
         */

        //使用组合
        System.out.println(method_andThen(
                i -> i * 2,
                i -> i + 2
        )); //先*2 在+2  结果是24


        System.out.println(method_ccompose(
                i -> i * 2,
                i -> i + 2
        )); //先+2 在*2  结果是26

        //返回输入参数的函数对象
        Object apply = Function.identity().apply(2);
        System.out.println(apply);
        
    }
}
```

## 3.方法引用
### 方法引用使得开发者可以将已经存在的方法作为变量来传递使用。方法引用可以和Lambda表达式配合使用。
``` java
public class MethodReferenceDemo {

    public static void main(String[] args) {

        //创建一个List , 将集合的元素翻转输出
        List<String> list = Arrays.asList("123","456","789");

        //1.通过普通lambda表达式实现
        List<String> list1 = DemoUtils.convert(list, i ->  new StringBuilder(i).reverse().toString());
        System.out.println(list1);

        //2.静态方法直接引用
        List<Integer> list2 = DemoUtils.convert(list, Integer::valueOf);
        System.out.println(list2);

        //3.非静态方法引用
        List<Integer> list3 = DemoUtils.convert(list, String::length);
        System.out.println(list3);

        //4.指定示例的方法引用
        Integer num = 2000;

        List<Integer> lists = Arrays.asList(1000,2000,3000);

        //lambda 方式
        List<Integer> results = DemoUtils.convert(lists, item->num.compareTo(item));
        System.out.println(results);

        //成员变量引用
        List<Integer> results2 = DemoUtils.convert(lists, num::compareTo);
        System.out.println(results2);

        //构造函数引用
        List<String> results3 = DemoUtils.convert(list, item -> LocalDate.now().toString());

        List<Date> results4 = DemoUtils.convert(lists, Date::new);
        //两种遍历方式
        results4.forEach(item->
            System.out.println(item.toString())
        );

        results4.forEach(System.out::println);
    }
}

```
