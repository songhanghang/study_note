#ota升级

>引用：OTA俗称（Over－the－Air Technology）空中下载技术。是通过移动通信（GSM或CDMA）的空中接口对SIM卡数据及应用进行远程管理的技术。空中接口可以采用WAP、GPRS、CDMA1X及短消息技术。OTA技术的应用，使得移动通信不仅可以提供语音和数据服务，而且还能提供新业务下载。


### Andoid系统升级
android系统升级有三种方式：

* 线刷：
 
>电脑端通过USB数据线连接手机，通过刷机软件，进行系统的写入操作

* 卡刷：

>下载update.zip升级文件，拷贝到设备Sdcard, 然后手动进入recovery刷入升级文件， 过程不依赖电脑
   
* ota升级:

> 通过Android OS 自动获取增量或者全量的update.zip,然后自动进行重新升级操作系统，ota方式升级较线刷&卡刷两种方式更加方便简单安全。
        
### apk 增量升级

>apk升级时只下载小内存增量文件，然后合并生成新的apk进行安装，节省用户流量，降低用户升级成本！
   
   [参见1: http://gold.xitu.io/post/57fba92abf22ec00649de645](http://gold.xitu.io/post/57fba92abf22ec00649de645)
   
   [参见2: http://blog.csdn.net/mynameishuangshuai/article/details/52744148](http://blog.csdn.net/mynameishuangshuai/article/details/52744148)
   
   
### 领先版需要的升级操作

  * 自身升级
     
>进入领先版检测是否有新版本，如果有弹窗提示用户升级app
        
  * 系统升级
  
>领先版调用EUI方法时，会先判断EUI系统的版本，如果运行所需要版本高于当前EUI版本时，会跳窗提示用户升级EUI系统版本.
    
  * 跳转app升级
  
>领先版跳转到对应的app以后，如：乐搜，进入乐搜以后，乐搜会检测自身是否有新版本升级，提示用户升级最新版本。
    
   
   
  
   

