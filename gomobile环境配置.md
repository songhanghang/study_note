#Golang Mobile 环境搭建
> 前言：Golang Mobile现为google实验性项目，尚未提供用户最终版。支持ios和android两个平台原生app和libs sdk开发，（原生app开发目前仅支持OPEN GL绘制界面），支持java、Object-C互调。

## 资料
github:  [https://github.com/golang/mobile](https://github.com/golang/mobile)

wiki: [https://github.com/golang/go/wiki/Mobile](https://github.com/golang/go/wiki/Mobile)

gobind: [https://godoc.org/golang.org/x/mobile/cmd/gobind](https://godoc.org/golang.org/x/mobile/cmd/gobind)

##1 安装gomobile工具
> 前提go环境已经搭建完成

###1.1 官方推荐安装方法
``` $ go get golang.org/x/mobile/cmd/gomobile ```

坑：需要科学上网，公司网络提示le.com证书失效，此方法未能下载安装。
###1.2 手动安装
1. ```$ git clone https://github.com/golang/mobile ```

   下载成功后改文件名为mobile, copy到$GOPATH/src/golang.org/x/ 
   
2.  ``` $ go build golang.org/x/mobile/cmd/gomobile ```

   build gomobile成功后会在$GOPATH/bin目录生成gomobile可执行程序，（执行过程中可能出现权限被拒，添加对应权限即可），copy生成的gomobile程序至/usr/local/go/bin/目录，此时便可以执行gomobile命令了。
   
##2 配置编译环境

``` $ gomobile init ```

  初始化环境，自动下载安装依赖，需要翻墙，可能会提示权限被拒，添加对应文件权限。

  
### android ndk配置
   可能会提示" No android NDK path is set "错误。解决办法如下:

   因gomobile init未同时下载ndk，所以需要手动下载ndk环境，然后执行
   
   ```$ gomobile init -ndk ~/Library/Android/sdk/ndk-bundle/```
   
   末尾路径为ndk安装路径，针对自己安装目录选择。
   
##3 编译 android demo
 ```  $ gomobile build -target=android golang.org/x/mobile/example/basic```
 
 
##4 运行 android demo
确定adb已经识别手机

```$ gomobile install golang.org/x/mobile/example/basic ``` 

安装成功后，即可打开这个可以随手势移动的三角形程序。


##总结：
 安装和环境配置这两个步奏，android和ios两个平台相同，编译和运行两个平台各不相同
