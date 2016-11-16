# 混淆注意事项

> 混淆保护文件路径： **../app/proguard-rules.pro**

1.  替换或者添加jar包时

    * 替换： 切记对比前后jar包名是否一致，是否有新添加的包名，如果有，务必重新添加keep保护
    
    * 添加：  切记添加新的keep保护jar不被混淆
    
2.  修改或者添加插件apk时
    * 修改： 在插件中修改代码，如果class中import新的类名，如下：
    
             * import com.letv.android.client.album.flow.model.AlbumStreamSupporter
    
     务必添加该import类的keep保护，如下：
    
           * -keep class com.letv.android.client.album.flow.model.AlbumStreamSupporter {*;}
    
           * -keep class com.letv.android.client.album.flow.model.AlbumStreamSupporter$* {*;}
    
    
    * 添加：
    
     切记使用插件keep自动生成工具去生成插件全部的keep信息
   
     工具目录:  **../Undependencies/ApkPluginProguardKeep** ( java工程用eclipse或者idea打开 )
    
    
    
3. 各module中涉及修改包名时

    * 务必到proguard-rules.pro中检查同步更改
