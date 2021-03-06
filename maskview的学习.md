
### maskView的初识

> 学习一些东西是因为感觉它有一些奇妙的地方,**maskView**正是如此

* 先给一些利用maskView做出来的效果图,满足自己的好奇的求知欲  好奇妙的赶脚
  ![](http://files.jb51.net/file_images/article/201608/2016812144516295.gif?2016712144619)

1.认识

> An optional view whose alpha channel is used to mask\(遮掩\) a view’s content.
> The view’s alpha channel determines how much of the view’s content and background shows through. Fully or partially opaque\(不透明的\) pixels allow the underlying content to show through but fully transparent\(透明的\) pixels block that content.

Availability
iOS \(8.0 and later\)

**概念词汇**

**阿尔法通道**\(Alpha Channel\) 阿尔法通道是一个8位的灰度通道，该通道用256级灰度来记录图像中的透明度信息，定义透明、不透明和半透明区域，其中黑表示透明，白表示不透明，灰表示半透明。

**Alpha通道作用 **
阿尔法通道\(Alpha Channel\)是指一张图片的透明和半透明度。

**个人理解:**
maskView就像是一个板子,上面有好多的空,你可以设置空的大小\(alpha\)来调节板子后面可见情况,下面举个栗子 \(图片来源简书作者:[http:\/\/www.jianshu.com\/p\/e4ac8266d9f0](http://www.jianshu.com/p/e4ac8266d9f0)\)

![](/assets/138050-6d105f8c1fd7336e.png)`图片1`

> 图1是一半纯灰色，一半 0.5 的绿， 图2背景是白色.

![](/assets/138050-5497241268ca44b4.png)

```图片2```


> 图片2是白色背景





```objc


self.view.backgroundColor = [UIColor blackColor];![](/assets/4D912E19-EA91-4D9A-9570-7D4F1D10832A.png)

 UIImageView * iamgeview = [[UIImageView alloc]initWithImage:[UIImage imageNamed:@"138050-5497241268ca44b4"]];

 iamgeview.frame =CGRectMake(50, 50, 300, 200);



 UIImageView * maskView = [[UIImageView alloc]initWithFrame:iamgeview.bounds];

 maskView.image = [UIImage imageNamed:@"138050-6d105f8c1fd7336e"];

 iamgeview.maskView = maskView;

 [self.view addSubview:iamgeview];



 UIImageView * iamgeview1 = [[UIImageView alloc]initWithFrame:CGRectMake(50, 300, 300, 200)];

 iamgeview1.image =[UIImage imageNamed:@"138050-5497241268ca44b4"];

 [self.view addSubview:iamgeview1];

```
![](/assets/4D912E19-EA91-4D9A-9570-7D4F1D10832A.png)



上面有了个初步的印象
下面实现以下最初给的图片碎片化效果
>原文
http://m.jb51.net/article/90384.htm

>在UIView中有一个maskView属性，这个属性是我们今天实现动画的最重要的变量。这个属性在iOS8之后开始使用，用来表示视图的遮罩。什么是遮罩呢？我想了很久都没有找到合适的比喻来介绍这个。简单来说，一个UIView的对象，可以通过设置alpha来改变这个视图的透明度，遮罩的实现效果也是一样的。唯一的差别在于前者是通过修改0~1之间的值来改变透明效果，作为遮罩的视图对象的**backgroundColor、alpha、transform**等等属性都会影响到被遮盖的视图的透明效果。

实现思路:将遮罩视图