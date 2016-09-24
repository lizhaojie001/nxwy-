# **Core Text**

## CoreText认识

> Core Text is an advanced, low-level technology for laying out text and handling fonts. The Core Text API, introduced in Mac OS X v10.5 and iOS 3.2, is accessible from all OS X and iOS environments.

**Important**: Core Text is intended for developers who must do text layout and font handling at a low level, such as developers of layout engines. You should develop your app using a higher-level framework if possible—that is, use Text Kit in iOS \(see Text Programming Guide for iOS\) or the Cocoa text system in OS X \(see a target="\_self" Cocoa Text Architecture Guide\/a\). Core Text is the technology underlying those text systems, so they share in its speed and efficiency. In addition, Text Kit and the Cocoa text system provide rich text editing, full-featured page layout engines, and other infrastructure that your app would otherwise need to provide if it used Core Text alone.

**CoreText** 是用于处理文字和字体的底层技术。它直接和 Core Graphics（又被称为 Quartz）打交道。Quartz 是一个 2D 图形渲染引擎，能够处理 OSX 和 iOS 中的图形显示。
Quartz 能够直接处理字体（font）和字形（glyphs），将文字渲染到界面上，它是基础库中唯一能够处理字形的模块。因此，CoreText 为了排版，需要将显示的文本内容、位置、字体、字形直接传递给 Quartz。相比其它 UI 组件，由于 CoreText 直接和 Quartz 来交互，所以它具有高速的排版效果。

![](/assets/1FF7A6E2-834E-4A77-9B5F-4543F4D64711.png)

下图是 CoreText 的架构图，可以看到，CoreText 处于非常底层的位置，上层的 UI 控件（包括 UILabel，UITextField 以及 UITextView）和 UIWebView 都是基于 CoreText 来实现的。

![](http://tangqiao.b0.upaiyun.com/coretext/coretext_arch.png)

> **Tips:**
> 在面向对象编程领域中，单一功能原则（Single responsibility principle）规定每个类都应该有一个单一的功能，并且该功能应该由这个类完全封装起来。所有它的（这个类的）服务都应该严密的和该功能平行（功能平行，意味着没有依赖）。
> 
> ## Core Text知识准备

#### 1.字符（Character）和字形（Glyphs）

> 排版系统中文本显示的一个重要的过程就是字符到字形的转换，字符是信息本身的元素，而字形是字符的图形表征，字符还会有其它表征比如发音。 字符在计算机中其实就是一个编码，某个字符集中的编码，比如Unicode字符集，就囊括了大都数存在的字符。 而字形则是图形，一般都存储在字体文件中，字形也有它的编码，也就是它在字体中的索引。 一个字符可以对应多个字形（不同的字体，或者同种字体的不同样式:粗体斜体等）；多个字符也可能对应一个字形，比如字符的连写（ Ligatures）。

![
](http://ww1.sinaimg.cn/large/65cc0af7gw1e2u5ypr899g.gif)
 Roman Ligatures

下面就来详情看看字形的各个参数也就是所谓的字形度量Glyph Metrics
![](http://ww3.sinaimg.cn/large/65cc0af7jw1e2ucdlrkfbg.gif)
![](http://ww2.sinaimg.cn/large/65cc0af7gw1e2u242uytyg.gif)

**bounding box（边界框 bbox）**，这是一个假想的框子，它尽可能紧密的装入字形。

**baseline（基线）**，一条假想的线,一行上的字形都以此线作为上下位置的参考，在这条线的左侧存在一个点叫做基线的原点，

**ascent（上行高度**）从原点到字体中最高（这里的高深都是以基线为参照线的）的字形的顶部的距离，ascent是一个正值

**descent（下行高度）**从原点到字体中最深的字形底部的距离，descent是一个负值（比如一个字体原点到最深的字形的底部的距离为2，那么descent就为-2）

**linegap（行距**），linegap也可以称作leading（其实准确点讲应该叫做External leading）,行高lineHeight则可以通过 ascent + descent + linegap 来计算。
####2.坐标系 

>首先不得不说 苹果编程中的坐标系花样百出，经常让开发者措手不及。 传统的Mac中的坐标系的原点在左下角，比如NSView默认的坐标系，原点就在左下角。但Mac中有些View为了其实现的便捷将原点变换到左上角，像NSTableView的坐标系坐标原点就在左上角。iOS UIKit的UIView的坐标系原点在左上角。  往底层看，Core Graphics的context使用的坐标系的原点是在左下角。而在iOS中的底层界面绘制就是通过Core Graphics进行的，那么坐标系列是如何变换的呢？ 在UIView的drawRect方法中我们可以通过UIGraphicsGetCurrentContext()来获得当前的Graphics Context。drawRect方法在被调用前，这个Graphics Context被创建和配置好，你只管使用便是。如果你细心，通过CGContextGetCTM(CGContextRef c)可以看到其返回的值并不是CGAffineTransformIdentity，通过打印出来看到值为

```
Printing description of contextCTM: (CGAffineTransform) contextCTM = { a = 1 b = 0 c = 0 d = -1 tx = 0 ty = 460 }

```

这是非retina分辨率下的结果，如果是如果是retina上面的a,d,ty的值将会乘2，如果是iPhone 5，ty的值会再大些。 但是作用都是一样的就是将上下文空间坐标系进行了flip，使得原本左下角原点变到左上角，y轴正方向也变换成向下。

上面说了一大堆，下面进入正题，Core Text一开始便是定位于桌面的排版系统，使用了传统的原点在左下角的坐标系，所以它在绘制文本的时候都是参照左下角的原点进行绘制的。 但是iOS的UIView的drawRect方法的context被做了次flip，如果你啥也不做处理，直接在这个context上进行Core Text绘制，你会发现文字是镜像且上下颠倒。
![](http://ww4.sinaimg.cn/large/65cc0af7gw1e2uwkpr6rfj.jpg)
所以在UIView的drawRect方法中的context上进行Core Text绘制之前需要对context进行一次Flip。
![](http://ww1.sinaimg.cn/large/65cc0af7gw1e2uwlh7zvej.jpg)
这里再提及一个函数CGContextSetTextMatrix，它可以用来为每一个显示的字形单独设置变形矩阵。

```objc
 CGContextSetTextMatrix(context, CGAffineTransformIdentity); CGContextTranslateCTM(context, 0, self.bounds.size.height); CGContextScaleCTM(context, 1.0, -1.0);

```

####3.NSMutableAttributedString 和 CFMutableAttributedStringRef 
>Core Foundation和Foundation中的有些数据类型只需要简单的强制类型转换就可以互换使用，这类类型我们叫他们为Toll-Free Bridged Types<https://developer.apple.com/library/content/documentation/CoreFoundation/Conceptual/CFDesignConcepts/Articles/tollFreeBridgedTypes.html>。  CFMutableAttributedStringRef和NSMutableAttributedString就是其中的一对，Core Foundation的接口基本是C的接口，功能强大，但是使用起来没有Foundation中提供的Objc的接口简单好使，所以很多时候我们可以使用高层接口组织数据，然后将其传给低层函数接口使用。
