Activity内存泄露分析

 Table of Contents Activity内存泄露分析什么是Activity内存泄露？如何检测内存泄露？Activity内存泄露case总结1. Activity实例作为context被static对象或者生命周期长于Activity的对象引用2. 匿名内部类间接持有外部类对象引用, 在Activity中匿名内部类使用不当易造成内存泄露3. Thread导致的内存泄露4. 匿名内部类使用了final修饰的context

什么是Activity内存泄露？

Activity内存泄露是指activity生命周期结束后, 依然被引用, 无法被gc释放, activity作为视图交互活动单元, 占用大量内存资源，比如：Views, AppContexts

如何检测内存泄露？

首先back键退出app到launcher界面,此时不应存在activity的泄露。

1. 最简单的方式通过adb命令查看内存信息，终端执行 'adb shell dumpsys meminfo 应用程序包名'。
   ➜  ~ adb shell dumpsys meminfo com.letv.android.client
   Objects
                  Views:      687         ViewRootImpl:        1
            AppContexts:        6           Activities:        1
                 Assets:        4        AssetManagers:        3
          Local Binders:       53        Proxy Binders:       44
          Parcel memory:      316         Parcel count:      547
       Death Recipients:        7      OpenSSL Sockets:        0
   Activities: 1 代表存在一个activity的泄露，此时已经离开全部activity，内存中不应存在activity实例
2. 使用android studio精确定位分析内存泄露

  

Activity内存泄露case总结



1. Activity实例作为context被static对象或者生命周期长于Activity的对象引用

- Application | Service 持有activity引用
- 单例持有activity引用
- static的Drawable (Drawable设置到view时,会设置callback指向view,view中索引context)

 修改方法: 千万小心static;  优先使用ApplicationContext代替Activity实例, 必须使用activity时一定要检查引用关系,注意离开时注销

2. 匿名内部类间接持有外部类对象引用, 在Activity中匿名内部类使用不当易造成内存泄露

- 回掉的callback生命周期长于Activity
- 观察者模式只有注册忘记注销 
- 匿名内部handler调用delay

 修改方法: 检查匿名内部类的生命周期, 修改使其和activity生命周期保持一致, 注册注销最好成对出现, handler应注意removeCallbacks

3. Thread导致的内存泄露

- 匿名内部线程,在Activity销毁时未执行结束
- 匿名内部线程中启动Looper,线程变成死循环，Activtity销毁时,looper未quit

 修改办法: Activity销毁时定要调用looper.quit(), 注意调用thread.interrupt()并不会关闭looper

4. 匿名内部类使用了final修饰的context

- 匿名内部类使用final修饰的对象时，编译时会在匿名内部类中拷贝一份对象引用，以便在脱离父类时正常使用

 修改方法: final修饰时，context尽量使用ApplicationContext,千万注意使用了final常量的对象的生命周期
