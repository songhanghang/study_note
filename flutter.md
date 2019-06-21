# Flutter 分享
> 参考： 
> [Flutter中文网](https://flutterchina.club/)
> [Flutter实战](https://book.flutterchina.club/)

## Hybrid简介
### H5 + 原生
WebView渲染界面，性能体验较差
### JS开发 + 原生 
代表框架React Native, Weex, 快应用, 原理都是JS去映射 Android | Ios 原生控件，原生渲染速度快于H5，受限于JS解释执行，速度无法追赶原生。
### 自研GUI + 原生
代表框架QT , Flutter, 渲染引擎直接对接不同平台系统API, 对外提供统一接口，性能和原生接近，但是动态性不足，需要编译发包。
> PS：QT在桌面端应用比较广泛，但是移动端因引擎大, C++开发效率低，坑多，推出即死。

## Flutter登场
Flutter是Google发布的一个用于创建跨平台、高性能移动应用的框架。Flutter和QT mobile一样，都没有使用原生控件，相反都实现了一个自绘引擎，使用自身的布局、绘制系统。
以下是官网介绍：
1. 快速开发
毫秒级的热重载，修改后，您的应用界面会立即更新。使用丰富的、完全可定制的widget在几分钟内构建原生界面.

2. 富有表现力和灵活的UI
快速发布聚焦于原生体验的功能。分层的架构允许您完全自定义，从而实现难以置信的快速渲染和富有表现力、灵活的设计。

3. 原生性能
Flutter包含了许多核心的widget，如滚动、导航、图标和字体等，这些都可以在iOS和Android上达到原生应用一样的性能。

4. 访问本地功能和SDK
通过平台相关的API、第三方SDK和原生代码让您的应用变得强大易用。 Flutter允许您复用现有的Java、Swift或ObjC代码，访问iOS和Android上的原生系统功能和系统SDK。

## Flutter思想
### 组合 > 集成
在Flutter中一切UI相关操作都是Widget，布局，容器，手势，滑动，功能都是Widget, 通过自由组合这些Widget来搭建功能和界面。
### 响应式编程
响应式编程一句话总结就是数据状态改变则UI随之自动改变，在React Native和Databing都是该思想， 在Flutter中表现的更加淋漓尽致。数据改变时通过setState() {}, 通知flutter framework执行rebuild widget -> update element -> render UI。

## Flutter核心原理

### 框架分层
Flutter框架是一个分层的结构，每个层都建立在前一层之上。使用Skia渲染引擎，因Android自带Skia, 所以打包Android不包含Skia, 这也是IOS体积较大的重要原因。
![](https://wx4.sinaimg.cn/mw690/006292TQly1g48yigwv61j30yu0iyq7m.jpg)

### 渲染流程
![](https://upload-images.jianshu.io/upload_images/5639324-4424415ff5f8681f.jpg?imageMogr2/auto-orient/)

### 三棵神树
![](https://upload-images.jianshu.io/upload_images/5639324-611c58af9c948150.jpg)
所有故事，从这个三棵树开始...
这三颗🌲分别是 Widget Tree, Element Tree, RenderObject Tree.

#### 第一颗Widget🌲
``` dart
/// Describes the configuration for an [Element].
abstract class Widget extends DiagnosticableTree {
  /// Initializes [key] for subclasses.
  const Widget({ this.key });
  final Key key;
  
  @protected
  Element createElement();

  /// A short, textual description of this widget.
  @override
  String toStringShort() {
    return key == null ? '$runtimeType' : '$runtimeType-$key';
  }

  @override
  void debugFillProperties(DiagnosticPropertiesBuilder properties) {
    super.debugFillProperties(properties);
    properties.defaultDiagnosticsTreeStyle = DiagnosticsTreeStyle.dense;
  }

  /// Whether the `newWidget` can be used to update an [Element] that currently
  static bool canUpdate(Widget oldWidget, Widget newWidget) {
    return oldWidget.runtimeType == newWidget.runtimeType
        && oldWidget.key == newWidget.key;
  }
}
```
Widget源码注释 `Describes the configuration for an [Element].`
Widget作用就是为Element描述需要的配置， 负责创建Element -> `createElement()`, 以及决定Element是否需要更新 -> `canUpdate(Widget oldWidget, Widget newWidget)`.
Flutter framework通过diff算法比对Widget树变化，然后决定Element的state改变。rebuild后的widget树，如果对比未发生改变， 则Element不会触发重绘，这就意味着widget树的重建并不会直接导致Element树重建。

#### 第二颗Element🌲

``` dart
/// An instantiation of a [Widget] at a particular location in the tree.
abstract class Element extends DiagnosticableTree implements BuildContext {
...
  /// The configuration for this element.
  @override
  Widget get widget => _widget;
  Widget _widget;
  
  /// The render object at (or below) this location in the tree.
  RenderObject get renderObject {
    ...
  }
...
}
```
Element源码注释： `An instantiation of a [Widget] at a particular location in the tree.`
Element表示Widget配置树特定位置的一个实例。持有widget和renderObject,负责组织管理widget配置和renderObject渲染。
可以理解为Android和IOS的View树，但是不同的是Element状态一般由Flutter framework管理， 开发者只需负责更改Widget即可。 

#### 第三颗RenderObject🌲

``` dart
/// An object in the render tree.
abstract class RenderObject extends AbstractNode with DiagnosticableTreeMixin implements HitTestTarget {
  ...
  @protected
  void performResize();

  @protected
  void performLayout();

  void paint(PaintingContext context, Offset offset);
  ...
```
RenderObject源码注释：  `An object in the render tree.`
RenderObject表示为渲染树的一个对象
顾名思义，RenderObject负责真正的渲染工作，需要自己定义绘制的话就要从此处切入, 测量大小 `performResize`，定位位置 `performLayout`， 绘制 `paint`都由RenderObject完成。

#### 总结
widget为配置树， element为UI控制树，renderobject为绘制树。开发者组合widget配置，系统framework通过比对widget配置来创新更新element，最后调度RenderObject渲染树进行上屏绘制。

## Widget 种类
Widget本身通常由许多更小的、单一用途widget组成，这些widget结合起来产生强大的效果。例如，Container是一个常用的widget， 由多个widget组成，这些widget负责布局、绘制、定位和调整大小。具体来说，Container由 LimitedBox、 ConstrainedBox、 Align、 Padding、 DecoratedBox、 和Transform组成。 您可以用各种方式组合这些以及其他简单的widget，而不是继承容器。

类层次结构很浅且很宽，可以最大限度地增加可能的组合数量。
![](https://wx1.sinaimg.cn/mw690/006292TQly1g48yigwyf5j315u0nwq58.jpg)

### Stateless Widget 
无状态Widget, 使用于不需要维护状态的场景，即创建后不可修改配置数据。

``` dart
abstract class StatelessWidget extends Widget {
  const StatelessWidget({ Key key }) : super(key: key);

  @override
  StatelessElement createElement() => StatelessElement(this);
  
  @protected
  Widget build(BuildContext context);
}
```
对应StatelessElement， 其中BuildContext即为对应的Element实例。
### StatefulWidget
![-w640](https://wx1.sinaimg.cn/mw690/006292TQly1g48yigw7ebj30zk0i4abe.jpg)

有状态widget, 通过setState(), 可以触发rebuild Widget, 从而刷新界面。
适用于数据改变需要刷新界面的场景。

``` dart
abstract class StatefulWidget extends Widget {
  const StatefulWidget({ Key key }) : super(key: key);

  @override
  StatefulElement createElement() => new StatefulElement(this);

  @protected
  State createState();
}

class StatefulElement extends ComponentElement {
  StatefulElement(StatefulWidget widget)
      : _state = widget.createState(),
        super(widget) {
       }());
    assert(_state._element == null);
    _state._element = this;
    assert(_state._widget == null);
    _state._widget = widget;
    assert(_state._debugLifecycleState == _StateLifecycle.created);
  }
}

```
对应StatefulElement， State在StatefulElement初始化时创建， State和StatefulElement相互持有，意味着State不会随着StatefulWidget重建, State持有StatefulWidget.


``` dart
class _PageWidgetState extends State<_PageWidget> {
  final GlobalKey<RefreshIndicatorState> _refreshIndicatorKey =
      GlobalKey<RefreshIndicatorState>();

  @override
  void initState() {
    super.initState();
    _refreshData();
  }

  Future _refreshData() async {
    Response response = await widget.page._request();
    setState(() {});
    return response;
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      key: _refreshIndicatorKey,
      onRefresh: _refreshData,
    
      ...
    );
  }
```

State通过setState(), 触发rebuild.

## 依赖管理

使用pubspec.yaml管理依赖，类似于Android gradle, IOS Cocoapods.
**eg:**
```
name: flutter_in_action
description: First Flutter application.

version: 1.0.0+1

dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^0.1.2

dev_dependencies:
  flutter_test:
    sdk: flutter

flutter:
  uses-material-design: true
```
## 资源管理
Flutter应用程序可以包含代码和 assets（有时称为资源）。assets是会打包到程序安装包中的，可在运行时访问。常见类型的assets包括静态数据（例如JSON文件）、配置文件、图标和图片（JPEG，WebP，GIF，动画WebP / GIF，PNG，BMP和WBMP）等。
Flutter也使用pubspec.yaml文件来管理应用程序所需的资源


```
  flutter:
  assets:
    - assets/my_icon.png
    - assets/background.png
```

## 工欲善其事必先利其器
环境配置略过...
### IDE插件
Flutter提供Android Studio和VS Code插件，这里只看Android Studio. 需下载flutter和dart插件, 安装后如图
![-w1148](https://wx2.sinaimg.cn/mw1024/006292TQly1g48yih40xgj30u00ym1kx.jpg)
### Code打开性能工具
还有一些性能工具需要通过代码打开。
``` dart
void main() {
    debugPaintSizeEnabled = true; // 显示文字基准线
    debugPaintPointersEnabled = true; // 突出点击对象
    debugPaintLayerBordersEnabled = true; // 显示层级边界
    debugRepaintRainbowEnabled = true; // 显示重绘
    runApp(MyApp());
}
```
### 一些有意思的工具

#### Select Widget Mode

滑动时显示触摸区域对应的Widget类型。

![](https://wx2.sinaimg.cn/mw690/006292TQly1g491cv5nf9g30a80fl7wl.gif)

#### Refresh Widget Info

刷新 rebuild widget。

#### Show Performance Overlay

显示每帧GPU绘制时间，和UI绘制时间，分别显示单帧最大绘制时间和平均绘制时间，debug模式JIT边解释边执行导致UI不能跑慢60帧每秒。

![](https://wx2.sinaimg.cn/mw690/006292TQly1g491nqk44og30ah0ilqv8.gif)

#### Toggle Platform

切换Android和IOS平台, 注意不只是TitleBar显示效果不同，转场动画也不相同。

![](https://wx1.sinaimg.cn/mw690/006292TQly1g4920glkqhg309r0hbe84.gif)

![](https://wx3.sinaimg.cn/mw690/006292TQly1g4920i5z51g30a40hykjo.gif)

#### Show Debug Paint

显示View边框

![](https://wx1.sinaimg.cn/mw690/006292TQly1g492agwkb1j30am0ixqbm.jpg)

#### Show Paint Baselines

显示Text基准线

![](https://wx1.sinaimg.cn/mw690/006292TQly1g492cebm1pj30am0ixagj.jpg)

#### debugPaintPointersEnabled

显示突出对象

![](https://wx3.sinaimg.cn/mw690/006292TQly1g492kv7r98j30am0ix43o.jpg)

#### debugPaintLayerBordersEnabled

显示层级边界

![](https://wx3.sinaimg.cn/mw690/006292TQly1g492n56azoj30am0ixn3a.jpg)

#### debugRepaintRainbowEnabled 

显示重绘

![](https://wx2.sinaimg.cn/mw690/006292TQly1g492qil7dwg30a40hy7wj.gif)


## DEMO 新闻客户端

[**https://github.com/songhanghang/flutter_news_demo**](https://github.com/songhanghang/flutter_news_demo)

![gif](https://wx2.sinaimg.cn/mw690/006292TQly1g3v1ygaisdg30dc0np1l0.gif)

源码：

``` dart
import 'dart:async';
import 'dart:convert';

import 'package:flutter/material.dart';
import 'package:flutter/rendering.dart';
import 'package:dio/dio.dart';
import 'package:flutter_webview_plugin/flutter_webview_plugin.dart';

Dio dio = Dio();

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: ScrollableTabsDemo(),
    );
  }
}

class _Page {
  _Page({this.text, this.key})
      : this.url = "https://3g.163.com/touch/reconstruct/article/list/$key/0-20.html";
  final String text;
  final String key;
  final String url;
  final List<_News> list = List();

  Future _request() async {
    Response response = await dio.get(url);
    String value = response.toString();
    value = value.substring(9, value.length - 1);
    Map<String, dynamic> map = json.decode(value);
    List data = map[key];
    list.clear();
    data.forEach((value) {
      list.add(_News.fromJson(value));
    });
    return response;
  }
}

class _News {
  final String title;
  final String url;
  final String skpUrl;
  final String image;
  final String digest;
  final String time;

  _News.fromJson(Map<String, dynamic> json)
      : title = json["title"],
        url = json["url"],
        skpUrl = json["skipURL"],
        image = json["imgsrc"],
        digest = json["digest"],
        time = json["ptime"];
}

List<_Page> _allPages = <_Page>[
  _Page(text: '新闻', key: "BBM54PGAwangning"),
  _Page(text: '娱乐', key: "BA10TA81wangning"),
  _Page(text: '体育', key: "BA8E6OEOwangning"),
  _Page(text: '财经', key: "BA8EE5GMwangning"),
  _Page(text: '军事', key: "BAI67OGGwangning"),
  _Page(text: '科技', key: "BA8D4A3Rwangning"),
  _Page(text: '手机', key: "BAI6I0O5wangning"),
  _Page(text: '数码', key: "BAI6JOD9wangning"),
  _Page(text: '时尚', key: "BA8F6ICNwangning"),
  _Page(text: '游戏', key: "BAI6RHDKwangning"),
  _Page(text: '教育', key: "BA8FF5PRwangning"),
  _Page(text: '健康', key: "BDC4QSV3wangning"),
  _Page(text: '旅游', key: "BEO4GINLwangning"),
];

class ScrollableTabsDemo extends StatefulWidget {
  @override
  _ScrollableTabsState createState() => _ScrollableTabsState();
}

class _ScrollableTabsState extends State<ScrollableTabsDemo>
    with SingleTickerProviderStateMixin {
  TabController _controller;

  @override
  void initState() {
    super.initState();
    _controller = TabController(vsync: this, length: _allPages.length);
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  Decoration _getIndicator() {
    return ShapeDecoration(
      shape: const RoundedRectangleBorder(
            borderRadius: BorderRadius.all(Radius.circular(8.0)),
            side: BorderSide(color: Colors.white70, width: 1.5),
          ) +
          const RoundedRectangleBorder(
            borderRadius: BorderRadius.all(Radius.circular(4.0)),
            side: BorderSide(color: Colors.transparent, width: 8.0),
          ),
    );
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: NestedScrollView(
        headerSliverBuilder: (BuildContext context, bool innerBoxIsScrolled) {
          return <Widget>[
            SliverOverlapAbsorber(
              handle: NestedScrollView.sliverOverlapAbsorberHandleFor(context),
              child: SliverAppBar(
                title: const Text('News'),
                pinned: false,
                backgroundColor: Colors.red,
                forceElevated: innerBoxIsScrolled,
                bottom: TabBar(
                  controller: _controller,
                  isScrollable: true,
                  labelColor: Colors.white,
                  indicator: _getIndicator(),
                  tabs: _allPages.map<Tab>((_Page page) {
                    return Tab(text: page.text);
                  }).toList(),
                ),
              ),
            ),
          ];
        },
        body: TabBarView(
          controller: _controller,
          children: _allPages.map<Widget>((_Page page) {
            return SafeArea(
              top: false,
              bottom: true,
              child: _PageWidget(page),
            );
          }).toList(),
        ),
      ),
    );
  }
}

class _PageWidget extends StatefulWidget {
  const _PageWidget(this.page, {Key key}) : super(key: key);
  final _Page page;

  @override
  State<StatefulWidget> createState() {
    return _PageWidgetState();
  }
}

class _PageWidgetState extends State<_PageWidget> {
  final GlobalKey<RefreshIndicatorState> _refreshIndicatorKey =
      GlobalKey<RefreshIndicatorState>();

  @override
  void initState() {
    super.initState();
    _refreshData();
  }

  Future _refreshData() async {
    Response response = await widget.page._request();
    setState(() {});
    return response;
  }

  @override
  Widget build(BuildContext context) {
    return RefreshIndicator(
      key: _refreshIndicatorKey,
      onRefresh: _refreshData,
      child: Scrollbar(
        child: ListView.builder(
          padding: kMaterialListPadding,
          itemCount: widget.page.list.length,
          itemBuilder: (BuildContext context, int index) {
            return GestureDetector(
              onTap: () {
                Navigator.push(context, MaterialPageRoute(builder: (context) {
                  String url = widget.page.list[index].url;
                  if (!url.startsWith("http")) {
                    url = widget.page.list[index].skpUrl;
                  }
                  return WebviewScaffold(
                      appBar: AppBar(title: Text("详情"), backgroundColor: Colors.red),
                      url: url);
                }));
              },
              child: Padding(
                  padding: EdgeInsets.all(8.0),
                  child: Row(
                    children: <Widget>[
                      DecoratedBox(
                        decoration: BoxDecoration(
                            borderRadius: BorderRadius.circular(3.0),
                            boxShadow: [
                              BoxShadow(
                                  color: Colors.black54,
                                  offset: Offset(2.0, 2.0),
                                  blurRadius: 4.0)
                            ]),
                        child: Image.network(
                          widget.page.list[index].image,
                          width: 140,
                          height: 80,
                          fit: BoxFit.cover,
                        ),
                      ),
                      Expanded(
                          flex: 1,
                          child: Padding(
                              padding: EdgeInsets.only(left: 10),
                              child: Align(
                                  alignment: Alignment.centerLeft,
                                  child: Wrap(
                                    children: <Widget>[
                                      Text(widget.page.list[index].title),
                                      Text(
                                        widget.page.list[index].digest,
                                        style: TextStyle(
                                            color: Colors.black87,
                                            fontSize: 10.0),
                                      ),
                                      Text(
                                        widget.page.list[index].time,
                                        style: TextStyle(
                                            color: Colors.black54,
                                            fontSize: 8.0),
                                      )
                                    ],
                                  ))))
                    ],
                  )),
            );
          },
        ),
      ),
    );
  }
}

```

