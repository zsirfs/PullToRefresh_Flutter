# pulltorefresh
[![pub package](https://img.shields.io/pub/v/pulltorefresh_flutter.svg)](https://pub.dartlang.org/packages/pulltorefresh_flutter)

上下拉控件，理论上适配所有可滑动View, Android IOS 双平台通用. 上下拉分别可控， 可单独使用上拉或下拉； 现已支持上下拉头完全自定义

如果使用当中有什么问题，请在github里提出个issues,thankYou

A control that make the ScrollView to be pull to refresh and push  to load data.Theoretically compatible with all Scrollable Widgets.RefreshBox now supports full customization

Pull and push are separately controllable,can Compatible with Android and IOS

If there is any problem with the use, please submit an issue in github,thankYou

HomePage：[https://github.com/baoolong/PullToRefresh_Flutter](https://github.com/baoolong/PullToRefresh_Flutter)

MoreWidght：[https://github.com/OpenFlutter/PullToRefresh](https://github.com/OpenFlutter/PullToRefresh)

My organization's github：[https://github.com/OpenFlutter](https://github.com/OpenFlutter)

<img width="38%" height="38%" src="https://raw.githubusercontent.com/baoolong/PullToRefresh/master/demonstrationgif/20180813170926.gif"/>

<img width="38%" height="38%" src="https://raw.githubusercontent.com/baoolong/PullToRefresh/master/demonstrationgif/20181023_90359.gif"/>

## Usage
Add this to your package's pubspec.yaml file:

	dependencies:
	  pulltorefresh_flutter: "^0.1.6"
	  
如果使用本项目默认图片，请下载https://raw.githubusercontent.com/baoolong/PullToRefresh_Flutter/master/images/refresh.png到你的images文件夹下，并在Pubspec.yaml添加如下配置
	  
If you want to use the default refresh image of this project (the rotated image), please download https://raw.githubusercontent.com/baoolong/PullToRefresh_Flutter/master/images/refresh.png to your images folder, and Pubspec.yaml is declared as follows.

     assets:
       - images/refresh.png

如果使用其他图片，请将图片放在Images文件夹下，在Pubspec.yaml文件中声明，并在PullAndPush对象中添加refreshIconPath属性，将图片路径设置给该属性

If you want to use other images, put the image in the Images folder, declare it in the Pubspec.yaml file, add the property "refreshIconPath" in the PullAndPush object,and set the image path to this property


Add it to your dart file:

	import 'package:pulltorefresh_flutter/pulltorefresh_flutter.dart';
	    
## Example
    class PullAndPushTest extends StatefulWidget{
    
      @override
      State<StatefulWidget> createState() {
        return new PullAndPushTestState();
      }
    }
    
    ///PullAndPush可以使用默认的样式，在此样式的基础上可以使用default**系列的属性改变显示效果，也可以自定义RefreshBox的样式（footerRefreshBox or headerRefreshBox）,也就是说可以定义其中一个，另一个用默认的，也可以全部自定义
    ///isPullEnable;isPushEnable属性可以控制RefreshBox 是否可用，无论是自定义的还是默认的
    ///PullAndPush can use the default style，Based on this style, you can use the properties of the default** series to change the display，
    ///You can also customize the style of the RefreshBox (footerRefreshBox or headerRefreshBox), which means you can define one of them, and the other can be customized by default or all.
    class PullAndPushTestState extends State<PullAndPushTest> with TickerProviderStateMixin{
      List<String> addStrs=["a","b","c","d","e","f","g","h","i","j","k","l","m","n","o","p","q","r","s","t","u","v","w","x","y","z"];
      List<String> strs=["1","2","3","4","5","6","7","8","9","0"];
      ScrollController controller=new ScrollController();
      //For compatibility with ios ,must use RefreshAlwaysScrollPhysics ;为了兼容ios 必须使用RefreshAlwaysScrollPhysics
      ScrollPhysics scrollPhysics=new RefreshAlwaysScrollPhysics();
      //使用系统的请求
      var httpClient = new HttpClient();
      var url = "https://github.com/";
      var _result="";
      String customRefreshBoxIconPath="images/icon_arrow.png";
      AnimationController customBoxWaitAnimation;
      double rotationAngle=0.0;
      String customHeaderTipText="快尼玛给老子松手！";
      String defaultRefreshBoxTipText="快尼玛给老子松手！";
    
    
      @override
      void initState() {
        super.initState();
        //这个是刷新时控件旋转的动画，用来使刷新的Icon动起来
        customBoxWaitAnimation=new AnimationController(duration: const Duration(milliseconds: 1000*100), vsync: this);
      }
    
      @override
      Widget build(BuildContext context) {
        return new Scaffold(
            appBar: new AppBar(
              title: new Text("上下拉刷新"),
            ),
            body: new PullAndPush(
              //如果你headerRefreshBox和footerRefreshBox全都自定义了，则default**系列的属性均无效，假如有一个RefreshBox是用默认的（在该RefreshBox Enable的情况下）则default**系列的属性均有效
              //If your headerRefreshBox and footerRefreshBox are all customizable，then the default** attributes of the series are invalid，
              // If there is a RefreshBox is the default（In the case of the RefreshBox Enable）then the default** attributes of the series are valid
              defaultRefreshBoxTipText: defaultRefreshBoxTipText,
              headerRefreshBox: new Container(
                  color: Colors.grey,
                  child:  new Row(
                    mainAxisAlignment: MainAxisAlignment.center,
                    children: <Widget>[
                      new Align(
                        alignment: Alignment.centerLeft,
                        child: new Transform(
                          origin: new Offset(45.0/2, 45.0/2),
                          transform: Matrix4.rotationX(rotationAngle),
                          child: new RotationTransition( //布局中加载时动画的weight
                            child: new ClipRect(
                              child: new Image.asset(
                                customRefreshBoxIconPath,
                                height: 45.0,
                                width: 45.0,
                              ),
                            ),
                            turns: new Tween(
                                begin: 100.0,
                                end: 0.0
                            )
                                .animate(customBoxWaitAnimation)
                              ..addStatusListener((animationStatus) {
                                if (animationStatus == AnimationStatus.completed) {
                                  customBoxWaitAnimation.repeat();
                                }
                              }
                              ),
                          ),
                        ),
                      ),
    
                      new Align(
                        alignment: Alignment.centerRight,
                        child:new ClipRect(
                          child:new Text(customHeaderTipText,style: new TextStyle(fontSize: 18.0,color: Color(0xffe6e6e6)),),
                        ),
                      ),
                    ],
                  )
              ),
    
              //你也可以自定义底部的刷新栏；you can customize the bottom refresh box
              animationStateChangedCallback:(AnimationStates animationStates,RefreshBoxDirectionStatus refreshBoxDirectionStatus){
                switch (animationStates){
                //RefreshBox高度达到50,上下拉刷新可用;RefreshBox height reached 50，the function of load data is  available
                  case AnimationStates.DragAndRefreshEnabled:
                    setState(() {
                      //3.141592653589793是弧度，角度为180度,旋转180度；3.141592653589793 is radians，angle is 180⁰，Rotate 180⁰
                      rotationAngle=3.141592653589793;
                    });
                    break;
    
                //开始加载数据时；When loading data starts
                  case AnimationStates.StartLoadData:
                    setState(() {
                      customRefreshBoxIconPath="images/refresh.png";
                      customHeaderTipText="正尼玛在拼命加载.....";
                    });
                    customBoxWaitAnimation.forward();
                    break;
    
                //加载完数据时；RefreshBox会留在屏幕2秒，并不马上消失，这里可以提示用户加载成功或者失败
                // After loading the data，RefreshBox will stay on the screen for 2 seconds, not disappearing immediately，Here you can prompt the user to load successfully or fail.
                  case AnimationStates.LoadDataEnd:
                    customBoxWaitAnimation.reset();
                    setState(() {
                      rotationAngle = 0.0;
                      if(refreshBoxDirectionStatus==RefreshBoxDirectionStatus.PULL) {
                        customRefreshBoxIconPath = "images/icon_cry.png";
                        customHeaderTipText = "加载失败！请重试";
                      }else if(refreshBoxDirectionStatus==RefreshBoxDirectionStatus.PUSH){
                        defaultRefreshBoxTipText="可提示用户加载成功Or失败";
                      }
                    });
                    break;
    
                //RefreshBox已经消失，并且闲置；RefreshBox has disappeared and is idle
                  case AnimationStates.RefreshBoxIdle:
                    setState(() {
                      defaultRefreshBoxTipText=customHeaderTipText="快尼玛给老子松手！";
                      customRefreshBoxIconPath="images/icon_arrow.png";
                    });
                    break;
                }
              },
              listView: new ListView.builder(
                //ListView的Item
                  itemCount: strs.length,//+2,
                  controller: controller,
                  physics: scrollPhysics,
                  itemBuilder: (BuildContext context,int index){
                    return new Container(
                      height: 35.0,
                      child: new Center(
                        child: new Text(strs[index],style: new TextStyle(fontSize: 18.0),),
                      ),
                    );
                  }
              ),
              loadData: (isPullDown) async{
                try {
                  var request = await httpClient.getUrl(Uri.parse(url));
                  var response = await request.close();
                  if (response.statusCode == HttpStatus.ok) {
                    _result = await response.transform(utf8.decoder).join();
                    setState(() {
                      //拿到数据后，对数据进行梳理
                      if(isPullDown){
                        strs.clear();
                        strs.addAll(addStrs);
                      }else{
                        strs.addAll(addStrs);
                      }
                    });
                  } else {
                    _result = 'error code : ${response.statusCode}';
                  }
                } catch (exception) {
                  _result = '网络异常';
                }
                print(_result);
              },
              scrollPhysicsChanged: (ScrollPhysics physics) {
                //这个不用改，照抄即可；This does not need to change，only copy it
                setState(() {
                  scrollPhysics=physics;
                });
              },
            )
        );
      }
    }

## Notice
1. 有时ListView的Item太少而不能铺满屏幕，导致ListView 不可Scroll，PullToRefresh也不可使用，同时为了兼容IOS，所以ScrollPhysics必须是RefreshAlwaysScrollPhysics。
2. 使用默认的样式可不用写headerRefreshBox和footerRefreshBox属性，若想自定义其中一个，则另一个为默认（默认状态下可用default**系列属性改变其外观：文字颜色等）
3. isPullEnable、isPushEnable属性可以控制RefreshBox 是否可用，无论是自定义的还是默认的

1. Sometimes the ListView has too few items to cover the screen, making the ListView not scrollable, PullToRefresh is not available , and for Compatible with IOS,so ScrollPhysics must be RefreshAlwaysScrollPhysics.
2. Use the default style without writing the headerRefreshBox and footerRefreshBox properties. If you want to customize one of them, the other is the default (the “ default** ” property can be changed by default in the default state: text color, etc.)
3. The isPullEnable and isPushEnable properties can control whether the RefreshBox is available, whether it is custom or default.

## Properties

#####     loadData
    加载数据的回调;callback of data loading
    
#####     scrollPhysicsChanged
    ListView的scrollPhysics发生改变时回调，通过SetState改变ListView的Physics， 这个回调直接抄demo里面的；
    the callback of when ListView’s scrollPhysics changed,please copy the callback from the demo

#####     listView
    用于上下拉的滑动控件;the widget for PullToRefresh

#####     isShowLeadingGlow
    在拉到顶部时，是否显示光晕效果,默认不显示；Whether to display the glow effect when pull，default is false

#####     isShowTrailingGlow
    在拉到底部时，是否显示光晕效果，默认不显示；Whether to display the glow effect when push，default is false

#####     backgroundColor
    自定义上下拉框的颜色; Customize the backgroundColor of the PullToRefresh box

#####     refreshIconPath
    自定义上下拉框中旋转的图片的路径; Customize the path of the rotated image in the PullToRefresh box

#####     tipText
    自定义上下拉框中的提示文字; Customize the text in the PullToRefresh box

#####     textColor
    自定义上下拉框中文字的颜色；Customize the color of the text in the PullToRefresh box

#####     isPullEnable
    是否使用下拉刷新，默认启用；Whether to use pull refresh, enabled by default;

#####     isPushEnable
    是否启用上拉加载，默认启用；Whether to enable push loading, enabled by default

#####     glowColor
    自定义光晕的颜色，默认为蓝色；Custom glow color, default is blue
    
####      headerRefreshBox and footerRefreshBox
    自定义头部Box和底部Box，Custom headerRefreshBox or footerRefreshBox
    
####      animationStateChangedCallback
    动画各种状态下的回调；Animation callbacks in various states
        DragAndRefreshEnabled,
        //RefreshBox高度达到50,上下拉刷新可用;RefreshBox height reached 50，the function of load data is  available
        
        StartLoadData,
        //开始加载数据时；When loading data starts
        
        LoadDataEnd,
        //加载完数据时；RefreshBox会留在屏幕2秒，并不马上消失，After loading the data，RefreshBox will stay on the screen for 2 seconds, not disappearing immediately
        
        RefreshBoxIdle
        //RefreshBox已经消失，并且闲置；RefreshBox has disappeared and is idle

## LICENSE
    MIT License

	Copyright (c) 2018 baoolong
	
	Permission is hereby granted, free of charge, to any person obtaining a copy
	of this software and associated documentation files (the "Software"), to deal
	in the Software without restriction, including without limitation the rights
	to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
	copies of the Software, and to permit persons to whom the Software is
	furnished to do so, subject to the following conditions:
	
	The above copyright notice and this permission notice shall be included in all
	copies or substantial portions of the Software.
	
	THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
	IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
	FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
	AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
	LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
	OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
	SOFTWARE.
