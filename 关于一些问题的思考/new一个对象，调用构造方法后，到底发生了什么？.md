## new一个对象，调用构造方法后，到底发生了什么？

在看spring循环依赖问题时，想到了这么一个情况

类A 依赖 类B，类B 又依赖 类A

如下

```java
public class A {
    private B b = new B();
    public A(){
        System.out.println("A已经创建");
    }
    public static void main(String[] args) {
        new A();
    }
}

public class B {
    private A a = new A();
    public B(){
        System.out.println("B已经创建");
    }
}
```

这样在main方法中new A()后，势必会产生循环依赖问题

然而报错却是产生了栈溢出，原因是反复的调用<init>

```
Exception in thread "main" java.lang.StackOverflowError
	at com.ly.test.B.<init>(B.java:8)
	at com.ly.test.A.<init>(A.java:11)
	at com.ly.test.B.<init>(B.java:8)
	at com.ly.test.A.<init>(A.java:11)
	......
```

- <init>是构造方法？
- new一个对象，调用构造方法，再对属性进行初始化(new)，又调用构造方法，如此循环就会栈溢出
- 问题好像解决了。但是明明是调用构造方法，为什么不先执行方法体中的语句，而是先对属性进行初始化呢？
- ==调用构造方法后到底发生了哪些事情？从JVM类加载的角度解释？==

