# Java 泛型擦除 + 可变参数 = 有坑

## Code


``` java
public abstract class A<T, P>{

     public A<T, P> exc(P... ps) {
         doTings(ps);
         return null;
     }

    void doTings(P... ps) {
    }
}



public class B<T> extends A<T, Void> {

    @Override
    void doTings(Void... voids) {

    }
}

```

声明A,B类，泛型作为可变参数执行

## Try Run
以下内容执行在Main类的main方法中，略去main
第一次点火，编译通过，运行炸了

``` java
B b = new B<String>();
b.exc();
```
第二次点火，编译通过，运行炸了

``` java
B b = new B();
b.exc();
```

第三次点火，编译通过，运行炸了

``` java
A b = new B();
b.exc();
```

第四次点火，**编译通过，运行通过**

``` java
B<String> b = new B<String>();
b.exc();
```

第五次点火，**编译通过，运行通过**

``` java
A<String, Void> b = new B<String>();
b.exc();
```

## 捉鬼...
### 先看炸点， WTF! 
炸在`B.doTings()`, 行数却显示在B类声明处`public class B<T> extends A<T, Void>`。
哪来的java.lang.Object[]，我绝对没有传！！！

`
Caused by: java.lang.ClassCastException: java.lang.Object[] cannot be cast to java.lang.Void[]
                                                                                   at com.example.garra.myapplication.B.doTings(B.java:7)
                                                                                   at com.example.garra.myapplication.A.exc(A.java:12)
                                                                                   at com.example.garra.myapplication.MainActivity.onCreate(MainActivity.java:14)
                                                                                   at android.app.Activity.performCreate(Activity.java:7057)
                                                                                   at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1214)
                                                                                   at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:2789)
                                                                                   at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:2911) 
                                                                                   at android.app.ActivityThread.-wrap11(Unknown Source:0) 
                                                                                   at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1608) 
                                                                                   at android.os.Handler.dispatchMessage(Handler.java:105) 
                                                                                   at android.os.Looper.loop(Looper.java:164) 
                                                                                   at android.app.ActivityThread.main(ActivityThread.java:6665) 
                                                                                   at java.lang.reflect.Method.invoke(Native Method) 
                                                                                   at com.android.internal.os.Zygote$MethodAndArgsCaller.run(Zygote.java:240) 
                                                                                   at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:781) `

### 猜想
定是泛型类型编译擦除搞得鬼，没准和可变参数的不定长特性也有关系？

### 反编译.class


```
@dalvik.annotation.Signature 
public abstract class A extends Object
{
    //======================== F I E L D S ==================
    //======================== C O N S T R U C T O R S ======
    public A() { ... }

    //======================== M E T H O D S ================

    @dalvik.annotation.Signature 
    transient void doTings(Object[]) { ... }
    @dalvik.annotation.Signature 
    public transient A exc(Object[]) { ... }

} 

@dalvik.annotation.Signature 
public class B extends A
{
    //======================== F I E L D S ==================
    //======================== C O N S T R U C T O R S ======
    public B() { ... }
    //======================== M E T H O D S ================

    volatile void doTings(Object[]) { ... }
    transient void doTings(Void[]) { ... }

} 

编译通过，运行通过

B b = new B();
b.exc(new Void[0]);


编译通过，运行炸了

B b = new B();
b.exc(new Object[0]);
```
可见编译后泛型全部擦除，替换成了实际的类型，注意到B类存在两个doTings()方法，Object[]和Void[]做为参数。

惊喜发现，`b.exc()`编译后出现了差异，我虽然没有传参数，但是编译器替我做了...

至此可以明白声明对象句柄时带不带泛型，会影响该类选择哪个方法执行。

But，Object[] cast to Void[] 又是从哪里执行的？


### 反编译.smali
A 类

``` smali
.class public abstract Lcom/example/garra/myapplication/A;
.super Ljava/lang/Object;
.source "A.java"

.annotation system Ldalvik/annotation/Signature;
    value = {
        "<T:",
        "Ljava/lang/Object;",
        "P:",
        "Ljava/lang/Object;",
        ">",
        "Ljava/lang/Object;"
    }
.end annotation

.method public constructor <init>()V
    .registers 1
    .local p0, this:Lcom/example/garra/myapplication/A;, "Lcom/example/garra/myapplication/A<TT;TP;>;"
    .prologue
    .line 9
    invoke-direct { p0 }, Ljava/lang/Object;-><init>()V
    return-void
.end method

.method varargs doTings([Ljava/lang/Object;)V
    .annotation system Ldalvik/annotation/Signature;
        value = {
            "([TP;)V"
        }
    .end annotation
    .registers 5
    .local p0, this:Lcom/example/garra/myapplication/A;, "Lcom/example/garra/myapplication/A<TT;TP;>;"
    .local p1, ps:[Ljava/lang/Object;, "[TP;"
    .prologue
    .line 18
    const-string/jumbo v0, "h"
    new-instance v1, Ljava/lang/StringBuilder;
    invoke-direct { v1 }, Ljava/lang/StringBuilder;-><init>()V
    const-string/jumbo v2, "dotings "
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
    move-result-object v1
    invoke-virtual { p1 }, Ljava/lang/Object;->getClass()Ljava/lang/Class;
    move-result-object v2
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/Object;)Ljava/lang/StringBuilder;
    move-result-object v1
    const-string/jumbo v2, ""
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
    move-result-object v1
    invoke-virtual { v1 }, Ljava/lang/StringBuilder;->toString()Ljava/lang/String;
    move-result-object v1
    invoke-static { v0, v1 }, Landroid/util/Log;->e(Ljava/lang/String;Ljava/lang/String;)I
    .line 19
    return-void
.end method

.method public varargs exc([Ljava/lang/Object;)Lcom/example/garra/myapplication/A;
    .annotation system Ldalvik/annotation/Signature;
        value = {
            "([TP;)",
            "Lcom/example/garra/myapplication/A",
            "<TT;TP;>;"
        }
    .end annotation
    .registers 5
    .local p0, this:Lcom/example/garra/myapplication/A;, "Lcom/example/garra/myapplication/A<TT;TP;>;"
    .local p1, ps:[Ljava/lang/Object;, "[TP;"
    .prologue
    .line 12
    const-string/jumbo v0, "h"
    new-instance v1, Ljava/lang/StringBuilder;
    invoke-direct { v1 }, Ljava/lang/StringBuilder;-><init>()V
    const-string/jumbo v2, "exc "
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
    move-result-object v1
    invoke-virtual { p1 }, Ljava/lang/Object;->getClass()Ljava/lang/Class;
    move-result-object v2
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/Object;)Ljava/lang/StringBuilder;
    move-result-object v1
    const-string/jumbo v2, ""
    invoke-virtual { v1, v2 }, Ljava/lang/StringBuilder;->append(Ljava/lang/String;)Ljava/lang/StringBuilder;
    move-result-object v1
    invoke-virtual { v1 }, Ljava/lang/StringBuilder;->toString()Ljava/lang/String;
    move-result-object v1
    invoke-static { v0, v1 }, Landroid/util/Log;->e(Ljava/lang/String;Ljava/lang/String;)I
    .line 13
    invoke-virtual { p0, p1 }, Lcom/example/garra/myapplication/A;->doTings([Ljava/lang/Object;)V
    .line 14
    const/4 v0, 0
    return-object v0
.end method

```
B 类


``` smali
.class public Lcom/example/garra/myapplication/B;
.super Lcom/example/garra/myapplication/A;
.source "B.java"

.annotation system Ldalvik/annotation/Signature;
    value = {
        "<T:",
        "Ljava/lang/Object;",
        ">",
        "Lcom/example/garra/myapplication/A",
        "<TT;",
        "Ljava/lang/Void;",
        ">;"
    }
.end annotation

.method public constructor <init>()V
    .registers 1
    .local p0, this:Lcom/example/garra/myapplication/B;, "Lcom/example/garra/myapplication/B<TT;>;"
    .prologue
    .line 7
    invoke-direct { p0 }, Lcom/example/garra/myapplication/A;-><init>()V
    return-void
.end method

.method bridge synthetic doTings([Ljava/lang/Object;)V
    .registers 2
    .local p0, this:Lcom/example/garra/myapplication/B;, "Lcom/example/garra/myapplication/B<TT;>;"
    .prologue
    .line 7
    check-cast p1, [Ljava/lang/Void;
    invoke-virtual { p0, p1 }, Lcom/example/garra/myapplication/B;->doTings([Ljava/lang/Void;)V
    return-void
.end method

.method varargs doTings([Ljava/lang/Void;)V
    .registers 2
    .local p0, this:Lcom/example/garra/myapplication/B;, "Lcom/example/garra/myapplication/B<TT;>;"
    .prologue
    .line 12
    return-void
.end method

```
重点看B类该方法

``` smali
.method bridge synthetic doTings([Ljava/lang/Object;)V
    .registers 2
    .local p0, this:Lcom/example/garra/myapplication/B;, "Lcom/example/garra/myapplication/B<TT;>;"
    .prologue
    .line 7
    check-cast p1, [Ljava/lang/Void;
    invoke-virtual { p0, p1 }, Lcom/example/garra/myapplication/B;->doTings([Ljava/lang/Void;)V
    return-void
.end method
```
这是个桥连方法，做了强制类型转换`check-cast p1`，这正是泛型擦除的核心实现，此时Object[] cast to Void[] 就炸了。

## 总结
正常使用泛型，不带可变参数时，会强制你传递指定类型的参数，所以不会出现此问题，但是遇上可变参数，可以什么也不传，这时编译器会根据规则自动选择参数类型，这样就有可能出现泛型强制转换类型时错误。


