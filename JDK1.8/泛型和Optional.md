# 泛型和Optional
## Optional
### 简介
Java应用中最常见的bug就是空值异常。
Optional仅仅是一个容器，可以存放T类型的值或者null。它提供了一些有用的接口来避免显式的null检查，可以参考Java 8官方文档了解更多细节。

## 使用
### 可能为空的值或者某个类型的值
``` java
public class OptionDemo {
    public static void main(String[] args) {

        /**
         * 如果Optional实例持有一个非空值，则isPresent()方法返回true，
         * 否则返回false；如果Optional实例持有null，
         * orElseGet()方法可以接受一个lambda表达式生成的默认值；
         * map()方法可以将现有的Optional实例的值转换成新的值,但是仅在不为空的情况
         * orElse()方法与orElseGet()方法类似，但是在持有null的时候返回传入的默认值，而不是通过Lambda来生成。
         */
        Optional<String> optional = Optional.ofNullable(null);
        Optional<String> optional1 = Optional.ofNullable("mike");
        System.out.println("isPresent : 为null " + optional.isPresent());
        System.out.println("isPresent : 不为null " + optional1.isPresent());

        System.out.println("orElsrGet 如果为null则返回: " + optional.orElseGet(()->"为空"));
        System.out.println("orElsrGet 如果不为null则返回: " + optional1.orElseGet(()->"为空"));

        //如果不为空才转换,为空则不转换
        System.out.println("map 方法转换: " + optional.map(s->"hello " + s + "!").orElse("为空,返回该结果"));
        System.out.println("map 方法转换: " + optional1.map(s->"hello " + s + "!").orElse("为空,返回该结果"));

        //需要注意of 方法不能为空 ofNullable 可以为空
        Optional< String > firstName = Optional.of( "Tom" );
        System.out.println( "First Name is set? " + firstName.isPresent() );
        System.out.println( "First Name: " + firstName.orElseGet( () -> "[none]" ) );
        System.out.println( firstName.map( s -> "Hey " + s + "!" ).orElse( "Hey Stranger!" ) );

        //Optional< String > firstName2 = Optional.of( null);

    }
}
```

## 泛型
### 简介
泛型，即“参数化类型”，就是将类型由原来的具体的类型参数化，类似于方法中的变量参数，此时类型也定义成参数形式（可以称之为类型形参），然后在使用/调用时传入具体的类型（类型实参）。
泛型的本质是为了参数化类型（在不创建新的类型的情况下，通过泛型指定的不同类型来控制形参具体限制的类型）。也就是说在泛型使用过程中，操作的数据类型被指定为一个参数，这种参数类型可以用在类、接口和方法中，分别被称为泛型类、泛型接口、泛型方法。
引入一个类型变量T（其他大写字母都可以，不过常用的就是T，E，K，V等等），并且用<>括起来，并放在类名的后面。泛型类是允许有多个类型变量的。

总结:   
1.适用于多种数据类型执行相同的代码  
2.泛型中的类型在使用时指定，不需要强制类型转换,减少java.lang.ClassCastException异常

### 常用泛型类型
- 泛型类
- 泛型接口
- 泛型方法  
值得注意的是，泛型的本质是参数化类型，所以在确定类型的情况下，实际上并不是泛型方法
``` java
   //这不是泛型方法，而是一个普通的方法，只是使用了Generic<Number>这个泛型类做形参而已。
    //obj这个参数的类型已经被定死了，是已经确定的类型：Generic<Number>
    public void showKeyValue1(Generic<Number> obj){
        Log.d("泛型测试","key value is " + obj.getKey());
    }
 
 
    //这也不是一个泛型方法，这也是一个普通的方法，只不过使用了泛型通配符?
    //泛型通配符?是一种类型实参（具体介绍可见泛型通配符一节），可以看做为所有类的父类
    //也就是说，这里的obj参数类型也是确定了的
    public void showKeyValue2(Generic<?> obj){
        Log.d("泛型测试","key value is " + obj.getKey());
    }
\
```
  
### 泛型通配符

#### 疑问
 
``` java
//Ingeter是Number的一个子类，类型擦除后Generic<Ingeter>与Generic<Number>实际上是相同的一种基本类型。那么问题来了，在使用Generic<Number>作为形参的方法中，能否使用Generic<Ingeter>的实例传入呢？在逻辑上类似于Generic<Number>和Generic<Ingeter>是否可以看成具有父子关系的泛型类型呢？
//验证
//Number类型的形参方法
public static Number showThisKey(Generic<Number> obj) {
    return obj.getKey();
}

//main方法中调用
public static void main(String[] args) {
    Generic<Number> number = new Generic<Number>();

    Generic<Integer> integer = new Generic<Integer>();

    showThisKey(number);

    //showThisKey(integer);  会报错

    /**
     * 通过提示信息我们可以看到Generic<Integer>不能被看作为Generic<Number>的子类。
     * 由此可以看出:同一种泛型可以对应多个版本（因为参数类型是不确定的），
     * 不同版本的泛型类实例是不兼容的。
     * 此时就要用到泛型通配符?
     */
}

//修改方法
//修改
public static Object showThisKey(Generic<?> obj) {
    return obj.getKey();
}

```
#### 总结
所有能用类型通配符（?）解决的问题都能用泛型方法解决，并且泛型方法可以解决的更好：

最典型的一个例子就是：

a. 类型通配符：void func(List<? extends A> list);

b. 完全可以用泛型方法完美解决：<T extends A> void func(List<T> list);

上面两种方法可以达到相同的效果（?可以代表范围内任意类型，而T也可以传入范围内的任意类型实参），并且泛型方法更进一步，?泛型对象是只读的，而泛型方法里的泛型对象是可修改的，即List<T> list中的list是可修改的！！

1) 要说两者最明显的区别就是：

   i. ?泛型对象是只读的，不可修改，因为?类型是不确定的，可以代表范围内任意类型；

   ii. 而泛型方法中的泛型参数对象是可修改的，因为类型参数T是确定的（在调用方法时确定），因为T可以用范围内任意类型指定；




