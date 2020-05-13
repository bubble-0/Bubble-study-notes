# default关键字

## 简介
&emsp;我们通常所说的接口的作用是用于定义一套标准、约束、规范等，接口中的方法只声明方法的签名，不提供相应的方法体，方法体由对应的实现类去实现,在JDK1.8中打破了这样的认识，接口中的方法可以有方法体，但需要关键字static或者default来修饰，使用static来修饰的称之为静态方法，静态方法通过接口名来调用，使用default来修饰的称之为默认方法，默认方法通过实例对象来调用。

## 静态方法和默认方法的作用  
&emsp;静态方法和默认方法都有自己的方法体，用于提供一套默认的实现，这样子类对于该方法就不需要强制来实现，可以选择使用默认的实现，也可以重写自己的实现。当为接口扩展方法时，只需要提供该方法的默认实现即可，至于对应的实现类可以重写也可以使用默认的实现，这样所有的实现类不会报语法错误：Xxx不是抽象的, 并且未覆盖Yxx中的抽象方法。

## 实例
### 创建一个接口IDefaultHello
```
package com.demo.bubble.NewCharacter.defaultdemo;

public interface IDefaultHello {

    void sayHello();

    static void staticSayHello(){
        System.out.println("static  hello");
    }

    default void defaultSayHello(){
        System.out.println("default  hello");
    }
}

```
### 创建实现类DefaultHelloImp,实现IDefaultHello接口并且调用接口中的方法
```
package com.demo.bubble.NewCharacter.defaultdemo;

public class DefaultHelloImp implements IDefaultHello{
    //不需要在去重写声明了static 和 default的方法
    @Override
    public void sayHello() {
        System.out.println("say hello");
    }

    public static void main(String[] args) {
        DefaultHelloImp dh = new DefaultHelloImp();

        //重写的方法可以直接使用
        dh.sayHello();

        //defaule方法可以直接使用
        dh.defaultSayHello();

        //静态方法只能通过接口名来调用
        IDefaultHello.staticSayHello();

    }
}
```
### 创建一个接口IDefaultHello2,DefaultHelloImp类实现IDefaultHello2接口
```
package com.demo.bubble.NewCharacter.defaultdemo;

public interface IDefaultHello2 {

    void sayHello();

    static void staticSayHello(){
        System.out.println("static  hello");
    }

    default void defaultSayHello(){
        System.out.println("default  hello");
    }
}


此时DefaultHelloImp就会报错,提示需要重写defaultSayHello 方法
```

## 结论
- default修饰的方法可以重写,也可以通过实例化一个对象来调用
- static修饰的方法只能通过接口名来调用
- 假如一个类实现了两个接口,里面含有两个default,static修饰的方法,那么default方法就需要重写,static方法不受影响,原因是因为default方法是实例化类来调用的如果实现了两个那么就不知道调用哪个接口中的方法,static只能通过接口名调用则不受影响