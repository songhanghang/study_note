#Activity内存泄露总结：

activity内存泄露是指activity生命周期结束后,依旧被引用,无法被gc释放

##1. Activity实例作为context被static对象或者生命周期长于activity的对象引用 

 * Application | Service 持有activity引用

 * 单例持有activity引用
 
 * static的Drawable (Drawable设置到view时,会设置callback指向view,view中索引context)

 **修改方法: 千万小心static; 优先使用ApplicationContext代替Activity实例,必须使用activity时一定要检查引用关系,注意离开时注销**

##2. 因匿名内部类间接持有外部类对象引用,在activity中使用匿名内部类易造成内存泄露

 * 回掉的callback

 * 观察者模式只有注册忘记注销 

 * 内部匿名handler调用delay
 
 **修改方法: 检查匿名内部类的生命周期,修改使其和activity生命周期保持一致,注册注销最好成对出现** 

##3. 线程导致的内存泄露 

 * 匿名内部线程,在activity销毁时未执行结束
 
 * 匿名内部线程中启动Looper,线程变成死循环，activtity销毁时,looper未quit
 
 **修改办法: activity销毁时定要调用looper.quit(), 注意调用thread.interrupt()并不会关闭looper**

##4. 匿名内部类使用了final修饰的context

 * 匿名内部类使用final修饰的对象时，编译时会在匿名内部类中拷贝一份对象引用，以便在脱离父类时正常使用

 **修改方法: final修饰时，context尽量使用ApplicationContext,千万注意使用了final常量的对象的生命周期**
