# **Core Text**

## CoreText认识

> Core Text is an advanced, low-level technology for laying out text and handling fonts. The Core Text API, introduced in Mac OS X v10.5 and iOS 3.2, is accessible from all OS X and iOS environments.

**Important**: Core Text is intended for developers who must do text layout and font handling at a low level, such as developers of layout engines. You should develop your app using a higher-level framework if possible—that is, use Text Kit in iOS \(see Text Programming Guide for iOS\) or the Cocoa text system in OS X \(see a target="\_self" Cocoa Text Architecture Guide\/a\). Core Text is the technology underlying those text systems, so they share in its speed and efficiency. In addition, Text Kit and the Cocoa text system provide rich text editing, full-featured page layout engines, and other infrastructure that your app would otherwise need to provide if it used Core Text alone.

**CoreText** 是用于处理文字和字体的底层技术。它直接和 Core Graphics（又被称为 Quartz）打交道。Quartz 是一个 2D 图形渲染引擎，能够处理 OSX 和 iOS 中的图形显示。
Quartz 能够直接处理字体（font）和字形（glyphs），将文字渲染到界面上，它是基础库中唯一能够处理字形的模块。因此，CoreText 为了排版，需要将显示的文本内容、位置、字体、字形直接传递给 Quartz。相比其它 UI 组件，由于 CoreText 直接和 Quartz 来交互，所以它具有高速的排版效果。



![](/assets/1FF7A6E2-834E-4A77-9B5F-4543F4D64711.png)

下图是 CoreText 的架构图，可以看到，CoreText 处于非常底层的位置，上层的 UI 控件（包括 UILabel，UITextField 以及 UITextView）和 UIWebView 都是基于 CoreText 来实现的。

![](http://tangqiao.b0.upaiyun.com/coretext/coretext_arch.png)
