# 自定义视图

> 有时为了从Controller里面将view抽离出来，并进一步封装成单独的UI模块，需要创建自定义的View，查了网上的相关资料，总结了大致有以下几种方法：

#### 1.代码是创建

**初始化：在初始化中添加所需要的所有控件**

```objc
 //重写系统的 
- (instancetype)initWithFrame:(CGRect)frame（Controller代码生成view时调用）

- (id)initWithCoder:(NSCoder *)aDecoder （Controller 通过XIB生成view时调用）
```

```objc

 //（或者）单独复写初始化，尽量用+方法 。

// 布局：下面两种组合是苹果官方推荐的做法，在drawRect中进行添加控件，在layoutSubviews布局

 //重写：
- (void)drawRect:(CGRect)rect

 （或者）重写：- (void)layoutSubviews 方法
```

**使用：**

* 代码调用，通过调用初始化方法生成自定义view.

* XIB调用：XIB中拉出UIVIEW，将该VIEW的class更改为自定义的view名称即可


#### 2.XIB方式创建

_1）创建类的XIB文件，命名为 类名.xib_
 ![](http://upload-images.jianshu.io/upload_images/1125888-a95a6d279a4d06aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

_2) 设置XIB的File's Owner为类名，size更改为Freeform, 然后可以在这个上面添加控件，并使用Autolayout
_
![](http://upload-images.jianshu.io/upload_images/1125888-6fa6e91e8ba29cdc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
_3）重写- (id)initWithCoder:(NSCoder *)aDecoder，添加如下代码：_
![](http://upload-images.jianshu.io/upload_images/1125888-46d3856afdbeb1f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
#### 调用：

 **1）XIB调用：XIB中拉出UIVIEW，将该VIEW的class更改为自定义的view名称即可**



