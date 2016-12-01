# 混淆注意事项

> 混淆保护文件路径： **../app/proguard-rules.pro**

### 警惕

1.  替换或者添加jar包时

    * 替换: 切记对比前后jar包名是否一致,是否有新添加的包名,如果有,务必重新添加keep保护
    
    * 添加:  切记添加新的keep保护jar不被混淆,或者添加对应jar的局部keep规则
    
2.  修改或者添加插件apk时
    * 修改： 在插件中修改代码，如果class中import新的类名，如下:
    
             * import com.letv.android.client.album.flow.model.AlbumStreamSupporter
    
     务必添加该import类的keep保护，如下：
    
           * -keep class com.letv.android.client.album.flow.model.AlbumStreamSupporter {*;}
    
           * -keep class com.letv.android.client.album.flow.model.AlbumStreamSupporter$* {*;}
    
    
    * 添加：
    
     切记使用插件keep自动生成工具去生成插件全部的keep信息
   
     工具目录:  **../Undependencies/ApkPluginProguardKeep** ( java工程用eclipse或者idea打开 )
    
    
    
3. 添加module、增加、修改包名时

    * 务必到proguard-rules.pro中检查同步更改

### 需要keep的内容
****
***HAS***    = 已实现，无需重复添加   
***WARING***  = 时刻警惕
****
1. 实体类    ***WARING***

        如无特殊情况,务必实现LetvBaseBean, 基类继承类已经全部keep
2. 第三方jar   ***WARING***

        如果第三方jar提供官方的keep规则,可以进行jar局部混淆.
        否则,keep jar内所有包名
3. 与js互相调用的类    ***WARING***
4. 反射类或者方法   ***WARING***
5. 实现序列化本地保存的类   ***HAS***
6. 所有Activity的类名   ***HAS***
7. View构造方法和Get Set    ***HAS***
8. 所有native jni调用方法   ***HAS***
9. 所有R类   ***HAS***

等...


### 编写proguard-rules
[5分钟搞定android混淆：http://www.jianshu.com/p/f3455ecaa56e](http://www.jianshu.com/p/f3455ecaa56e)


