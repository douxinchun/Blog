---
layout: post
title: "iOS开发过程中遇到的Bug处理集合"
date: 2015-03-27 15:00:07 +0800
comments: true
categories: iOS开发过程中遇到的坑
---  

##1.iOS7 下UIScrollView 无法滑动的解决办法  

环境:Xcode6.2 iOS7 开启了AutoLayout 在Storyboard中拖入的一个UIScrollview  
接收的别人的code,在这个基础上开发了一个新的界面,然后手贱脑残的想用一下AutoLayout练练手(6和6+都出了,感觉以后使用AutoLayout会是主流),刚开始一切OK,跑起来顺畅的很,在3.5和4的屏幕上适应的都不错,后来不知道怎么调整了一下UI,结果悲剧发生了,Scrollview死活滑动不了了,导致Scrollview上的下拉刷新也不好用了.  
第一反应是AutoLayout的问题,禁用了AutoLayout,Run,一切OK,能滑动了.但是这丫的是Storyboard啊,一个ViewControll禁用课AutoLayout,整个文件都禁用.Google解决方案,结果果然受AutoLayout的影响,然后解决方案都是说要在viewdidAppear中重设contentSize,好吧,把contentSize的设定从viewdidload移到viewdidAppear中,Run,Fail... ...Fuck Google上清一水的解决方案都是这个,说好的滑动呢...  
<!--more-->
最后,纠结了1分钟,在viewdidload,viewwillapear,viewdidappear,viewlayoutsubviews中都设置一次contentSize,Run,神奇的事情发生了,Scrollview动起来了,然后一个一个的去除contentSize,最终确定,只有在viewlayoutsubviews设置才会有效,,坑爹啊.  
具体的code如下:  
{% codeblock lang:objc viewDidLayoutSubviews %}
// Called just before the view controller's view's layoutSubviews method is invoked. Subclasses can implement as necessary. The default is a nop.
- (void)viewWillLayoutSubviews{
    NSLog(@"viewWillLayoutSubviews");
    scrollview.contentSize = CGSizeMake(CGRectGetWidth(self.view.frame), CGRectGetHeight(self.view.frame)+1);
    
}
{% endcodeblock %}

坑爹啊,问题虽然解决了,但是这不科学啊,于是重新查看UIViewController的生命周期:  
```
2015-03-27 16:17:35.887 BugDemo[3195:937760] viewDidLoad
2015-03-27 16:17:35.888 BugDemo[3195:937760] viewWillAppear
2015-03-27 16:17:35.891 BugDemo[3195:937760] viewWillLayoutSubviews
2015-03-27 16:17:35.892 BugDemo[3195:937760] viewDidLayoutSubviews
2015-03-27 16:17:35.900 BugDemo[3195:937760] viewDidAppear
```  
发现生命周期的最后一个方法是viewDidAppear,只好理解为涉及了Autolayout,在viewDidAppear中设置已经晚了,可能是由于AutoLayout的某些原因,到了viewDidAppear的时候再设置contentSize属性已经不被scrllview接受或者被AutoLayout的某些机制给拦截了重设为另一个自适应的数值了.  
最后,总结,AutoLayout真是坑,界面还是乖乖的用code手写吧,这样出了问题好定位分析,解决.  

##2.UIScrollView不能响应UITouch事件的解决办法  

原因是：UIView的touch事件被UIScrollView捕获了。

解决办法：让UIScrollView将事件传递过去。于是最简单的解决办法就是加一个UIScrollView的category。这样每个用到UIScrollView的地方只要导入这个category就可以直接响应相关的touch事件了。

{% codeblock lang:objc UIScrollView+UITouch %}
#import "UIScrollView+UITouch.h"       
 @implementation UIScrollView (UITouch)       
 - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event {   
   [[self nextResponder] touchesBegan:touches withEvent:event];   
   [super touchesBegan:touches withEvent:event];   
 }       
 -(void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event {   
   [[self nextResponder] touchesMoved:touches withEvent:event];   
   [super touchesMoved:touches withEvent:event];   
 }   
 - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event {   
   [[self nextResponder] touchesEnded:touches withEvent:event];   
   [super touchesEnded:touches withEvent:event];   
 }   
 @end 
{% endcodeblock %}  

##3.UITableview点击背景隐藏键盘  
由于UITableview是UIScrollview的子类,如果按照2中的方式话,会响应touchbegin方法,但是UITablevieDelegate中的didSelectRowAtIndexPath就会不响应了.  
解决方法参照:http://stackoverflow.com/questions/2321038/dismiss-keyboard-by-touching-background-of-uitableview  
{% codeblock lang:objc%}
UITapGestureRecognizer *gestureRecognizer = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(hideKeyboard)];
[self.tableView addGestureRecognizer:gestureRecognizer];

//The UITapGestureRecognizer solution works with table cell selection if you set:
//加入这一句,不会影响到tableview的didSelectRowAtIndexPath方法
gestureRecognizer.cancelsTouchesInView = NO;

{% endcodeblock %}  
然后,添加hideKeyboard方法,想下面这样  
{%codeblock%}
- (void) hideKeyboard {
    [self.view endEditing:YES];
    ...
    ...
}
{%endcodeblock%}  

此外,还有一种可以响应touchbegin的方案参见这里 http://www.cnblogs.com/tangbinblog/p/4066930.html ,不过这种方案看上去需要以子类的方式重写hitTest方法,暂时没有考证可行性.先Mark一下,等以后再来看看.

