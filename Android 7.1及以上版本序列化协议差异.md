## 实验结论

结论先行
Android 7.1及以上版本针对变量名字不变但是类型变化的class反序列化并不会抛异常！！！

待补：
1. 不同版本此差异的具体原因分析
2. 标准的序列化协议到底应该是怎么样的？这算是个bug吗？

* 序列化类

```java
class A implements Serializable {
   private static final long serialVersionUID = 1;
   private final B test；// !!! 注意序列化的test变量类型为B!!!
   public A(B test) {
     this.test = test;
   }
}
// 序列化
outputStream.writeObject(new A(new B()))
```
* 反序列化类

```java
class A implements Serializable {
   private static final long serialVersionUID = 1;
   private final C test； // !!! 注意变量名字test不变，类型由B改为C !!!
   public A(C test) {
     this.test = test;
   }
}
// 反序列化
A a = (A) inputStream.readObject()
```

以上代码在JVM虚拟机和Android 7.1以下版本，反序列化时会抛test类型转化错误异常,

但是Android 7.1及以上版本并不会抛任何异常，A会成功反序列化，只是test = null。

先看下Android 6.0 readObject执行trace

``` java
at java.lang.reflect.Field.set(Native Method)                              
at java.lang.reflect.Field.set(Field.java:557)                             
at java.io.ObjectInputStream.readFieldValues(ObjectInputStream.java:1127)  
at java.io.ObjectInputStream.defaultReadObject(ObjectInputStream.java:454) 
at java.io.ObjectInputStream.readObjectForClass(ObjectInputStream.java:1345
at java.io.ObjectInputStream.readHierarchy(ObjectInputStream.java:1242)    
at java.io.ObjectInputStream.readNewObject(ObjectInputStream.java:1835)    
at java.io.ObjectInputStream.readNonPrimitiveContent(ObjectInputStream.java
at java.io.ObjectInputStream.readObject(ObjectInputStream.java:1983)       
at java.io.ObjectInputStream.readObject(ObjectInputStream.java:1940)       
```

对比下Android 7.1 readObject执行trace


```java
at java.io.ObjectStreamClass$FieldReflector.setObjFieldValues(ObjectStreamClass.java:2082)
at java.io.ObjectStreamClass.setObjFieldValues(ObjectStreamClass.java:1250)
at java.io.ObjectInputStream.defaultReadFields(ObjectInputStream.java:1998)
at java.io.ObjectInputStream.readSerialData(ObjectInputStream.java:1916)
at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:1799)
at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1351)
at java.io.ObjectInputStream.readObject(ObjectInputStream.java:373)
```

具体还有哪些行为不同，现在尚不可知！

回过来再看下Android Serializable接口文档
[https://developer.android.com/reference/java/io/Serializable](https://developer.android.com/reference/java/io/Serializable)


If a serializable class does not explicitly declare a serialVersionUID, then the serialization runtime will calculate a default serialVersionUID value for that class based on various aspects of the class, as described in the Java(TM) Object Serialization Specification. However, it is strongly recommended that all serializable classes explicitly declare serialVersionUID values, since the default serialVersionUID computation is highly sensitive to class details that may vary depending on compiler implementations, and can thus result in unexpected InvalidClassExceptions during deserialization. Therefore, to guarantee a consistent serialVersionUID value across different java compiler implementations, a serializable class must declare an explicit serialVersionUID value. It is also strongly advised that explicit serialVersionUID declarations use the private modifier where possible, since such declarations apply only to the immediately declaring class--serialVersionUID fields are not useful as inherited members. Array classes cannot declare an explicit serialVersionUID, so they always have the default computed value, but the requirement for matching serialVersionUID values is waived for array classes. ***Android implementation of serialVersionUID computation will change slightly for some classes if you're targeting android N. In order to preserve compatibility, this change is only enabled is the application target SDK version is set to 24 or higher.*** It is highly recommended to use an explicit serialVersionUID field to avoid compatibility issues.

注意粗体：Android 7.0 （API 24）及以上版本serialVersionUID计算实现稍微更改了下...

不知此差异是否是google有意为之？

但是变量名字相同 类型却不同的数据反序列化一旦成功，就会导致结果为null的脏数据逃逸到正常流程...

## 会引起什么问题？

一般主动更改变量类型时，会主动更改serialVersionUID，并不会引起什么问题，
但是有个特例，
混淆，
app不同版本的同一个类的混淆结果路径可能不一致！
即使你未修改代码，但是混淆会帮你改!
我手继承自Serializable的类名又是允许被混淆的...

假设高低版本未修改过的同一个类
* 混淆前

```java
class Bean implements Serializable {
   private static final long serialVersionUID = 1;
   private final AdInfo test；
   public Bean(AdInfo test) {
     this.test = test;
   }
}
```

* 旧版本混淆后

```java
class A implements Serializable {
   private static final long serialVersionUID = 1;
   private final B test；// !!! 注意变量名字不变，AdInfo 混淆成了 B!!!
   public A(B test) {
     this.test = test;
   }
}
```

* 新版本混淆后

```java
class A implements Serializable {
   private static final long serialVersionUID = 1;
   private final C test；// !!! 注意变量名字不变，AdInfo 混淆成了 C !!!
   public A(C test) {
     this.test = test;
   }
}
```

版本升级的时候类的混淆名字发生变化，但是持久化在本地的名字还是上一个版本的名字，因为两个包混淆名不相同导致反序列化的时候test为null，Android 7.1 以下是报错丢弃该数据，7.1 及以上版本是test = null。
7.1以上就出现逃逸的脏数据，test会诡异的变成null, 如果test变量在代码中要求非空注入，则反序列化后的脏数据将导致各种不合预期的错误！





