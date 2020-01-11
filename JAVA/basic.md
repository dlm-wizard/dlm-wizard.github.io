## 快速开始

- [一、数据类型](#一数据类型)
  - [基本类型](#基本类型)
  - [包装类型](#包装类型)
  - [缓存池](#缓存池)
- [二、JAVA 关键字](#二JAVA关键字)

[Google Java规范](https://google.github.io/styleguide/javaguide.html#s4.8.7-modifiers)

What is a reasonable order of Java modifiers?

<div align="center"><img src="Java关键字顺序" width="75%" height="75%" /></div><br>

# 一、数据类型

## 基本类型

- byte/8
- short/16
- int/32
- long/64`后缀L、十六进制前缀 0x、八进制前缀 0、二进制数前缀 0b`

```
以上存储类型为 signed 补码

Java 中整形运行范围与运行 Java 机器无关，解决软件移植问题。

C / C++ 需要针对不同处理器【32位 or 16位】，数值类型占字节数与平台相关
```

- float/32`后缀F`
- double/64

```bash
遵循 IEEE 754 规范

Double.POSITIVE_INFINITY = 1.0 / 0.0;
Double.NEGATIVE_INFINITY = -1.0 / 0.0;
Double.NaN # 非数字

金融计算禁止使用浮点数值，应该使用 BigDecimal 类
```

- char/16 `UTF-16 描述 Unicode 值「16进制」对应的字符`

```
char foo = '\u0041';
System.out.println(foo); // => A
```

- boolean/\~

```
1. 可以用 1 bit 来存储，具体大小未明确规定

2. JVM 会在编译时期将 boolean 类型的数据转换为 int，使用 1 来表示 true，0 表示 false。

3. JVM 支持 boolean 数组，但是是通过读写 byte 数组来实现的。
```


- [Primitive Data Types](https://docs.oracle.com/javase/tutorial/java/nutsandbolts/datatypes.html)
- [The Java® Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se8/jvms8.pdf)

## 包装类型

基本类型都有对应的包装类型，基本类型与其对应的包装类型之间的赋值使用自动装箱与拆箱完成。

```java
Integer x = 2;     // 装箱 调用了 Integer.valueOf(2)
int y = x;         // 拆箱 调用了 X.intValue()
```

- [Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)

## 缓存池

new Integer(123) 与 Integer.valueOf(123) 的区别在于：

- new Integer(123) 每次都会新建一个对象；
- Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。

```java
Integer x = new Integer(123);
Integer y = new Integer(123);
System.out.println(x == y);    // false
Integer z = Integer.valueOf(123);
Integer k = Integer.valueOf(123);
System.out.println(z == k);   // true
```

valueOf() 方法的实现比较简单，就是先判断值是否在缓存池中，如果在的话就直接返回缓存池的内容。

```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```

在 Java 8 中，Integer 缓存池的大小默认为 -128\~127。

```java
static final int low = -128;
static final int high;
static final Integer cache[];

static {
    // high value may be configured by property
    int h = 127;
    String integerCacheHighPropValue =
        sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
    if (integerCacheHighPropValue != null) {
        try {
            int i = parseInt(integerCacheHighPropValue);
            i = Math.max(i, 127);
            // Maximum array size is Integer.MAX_VALUE
            h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
        } catch( NumberFormatException nfe) {
            // If the property cannot be parsed into an int, ignore it.
        }
    }
    high = h;

    cache = new Integer[(high - low) + 1];
    int j = low;
    for(int k = 0; k < cache.length; k++)
        cache[k] = new Integer(j++);

    // range [-128, 127] must be interned (JLS7 5.1.7)
    assert IntegerCache.high >= 127;
}
```

编译器会在自动装箱过程调用 valueOf() 方法，因此多个值相同且值在缓存池范围内的 Integer 实例使用自动装箱来创建，那么就会引用相同的对象。

```java
Integer m = 123;
Integer n = 123;
System.out.println(m == n); // true
```

基本类型对应的缓冲池如下：

- boolean values true and false
- all byte values
- short values between -128 and 127
- int values between -128 and 127
- char in the range \u0000 to \u007F

在使用这些基本类型对应的包装类型时，如果该数值范围在缓冲池范围内，就可以直接使用缓冲池中的对象。

在 jdk 1.8 所有的数值类缓冲池中，Integer 的缓冲池 IntegerCache 很特殊，这个缓冲池的下界是 - 128，上界默认是 127，但是这个上界是可调的，在启动 jvm 的时候，通过 -XX:AutoBoxCacheMax=&lt;size&gt; 来指定这个缓冲池的大小，该选项在 JVM 初始化的时候会设定一个名为 java.lang.IntegerCache.high 系统属性，然后 IntegerCache 初始化的时候就会读取该系统属性来决定上界。

[StackOverflow : Differences between new Integer(123), Integer.valueOf(123) and just 123
](https://stackoverflow.com/questions/9030817/differences-between-new-integer123-integer-valueof123-and-just-123)

- [`[API]` 参考](#参考)

[如何通俗易懂地举例说明「面向对象」和「面向过程」有什么区别？](https://www.zhihu.com/question/27468564)

**[What is the difference between JDK and JRE?](https://stackoverflow.com/questions/1906445/what-is-the-difference-between-jdk-and-jre)**

- **JRE** is the JVM program、libraries and other components to run applets and applications, Java application need to run on JRE.
- **JDK** is a superset of JRE, JRE + tools for developing java programs. e.g, it provides the compiler "javac"

如果你只是为了运行一下 Java 程序的话，那么你只需要安装 JRE 就可以了。如果你需要进行一些 Java 编程方面的工作，那么你就需要安装 JDK 了。但是，这不是绝对的。有时，即使您不打算在计算机上进行任何 Java 开发，仍然需要安装 JDK。例如，如果要使用 JSP 部署 Web 应用程序，那么从技术上讲，您只是在应用程序服务器中运行 Java 程序。那你为什么需要 JDK 呢？因为应用程序服务器会将 JSP 转换为 Java servlet，并且需要使用 JDK 来编译 servlet。

<div align="center">  <img src="编译执行" width=""/></div><br>

## 二、JAVA 关键字

### final

- 数据

ES6 const - 使值或引用不变

声明数据为常量，可以是编译时常量，也可以在运行时被初始化后不能被修改的常量

```java
final int a = 1;
// a = 2;  // cannot assign value to final variable 'a'
final A b = new A();
b.x = 1;
```

- 方法

```bash
不能被子类重写（private 方法隐式地被指定为 final）
```

如果在子类中定义的方法和基类中的一个 private 方法签名相同，此时子类不是重写基类方法，而是在子类中定义了一个新的方法。

- 类

```bash
不能被继承
```

### static

- 静态变量（类变量）

类所有实例共享，可以直接通过类名访问（静态变量在内存中只存在一份）

每创建一个实例就会产生一个实例变量（实例变量与该实例同生共死）

```java
public class A {

    private int x;         // 实例变量
    private static int y;  // 静态变量

    public static void main(String[] args) {
        // int x = A.x;  // Non-static field 'x' cannot be referenced from a static context
        A a = new A();
        int x = a.x;
        int y = A.y;
    }
}
```

- 静态方法

在类加载的时候就存在了，不依赖于任何实例。所以静态方法必须有实现，也就是说它不能是抽象方法。

只能访问所属类的静态字段和静态方法，方法中不能有 this、super 关键字

```java
public abstract class A {
    private static int x;
    private int y;

    public static void func1(){
        int a = x;
        // int b = this.y;     // 'A.this' cannot be referenced from a static context
    }

    // public abstract static void func2();  // Illegal combination of modifiers: 'abstract' and 'static'
}
```

- 静态语句块

静态语句块只在类初始化时运行一次。

```
public class A {
    static {
        System.out.println("123");
    }

    public static void main(String[] args) {
        A a1 = new A();
        A a2 = new A();
    }
}
// => 123 -- Print once
```

- [静态内部类](https://github.com/CyC2018/CS-Notes/blob/master/notes/Java%20%E5%9F%BA%E7%A1%80.md#static)

- 静态导包

在使用静态变量和方法时不用再指明 ClassName，从而简化代码，但**可读性大大降低**。

```
// 导入所有静态方法
import static com.xxx.ClassName.*
```

- 初始化顺序

静态变量和静态语句块优先于实例变量和普通语句块。静态变量与静态语句块初始化取决于它们在代码之中的顺序。

```
public class A {

    public static String staticField = "静态变量";
    public String field = "实例变量";

    static {
        System.out.println("静态语句块");
    }

    System.out.println("普通语句块");

    public A() {
        System.out.println("构造函数");
    }
}
// 在存在继承的情况下，初始化顺序为：
// => 父类（静态变量、静态语句块）
// => 子类（静态变量、静态语句块）
// => 父类（实例变量、普通语句块）
// => 父类（构造函数）
// => 子类（实例变量、普通语句块）
// => 子类（构造函数）
```

- 类

```bash
不能被继承
```

##

## 参考

[Java 学习路线](https://www.zhihu.com/question/307096748/answer/621651379)

[常用 Java 资源](https://shimo.im/docs/CPB0PK05rP4CFmI2/read)
