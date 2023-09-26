---
layout: post
title: "AutoLayout 使用总结"
date: 2015-07-20 16:55:20 +0800
comments: true
categories: iOS
---  

###1.如何计算UITableViewCell的高度  
在IOS的布局中，计算和适应cell的高度是个经典的问题, 在frame时代(springs和struts方式布局时代)，我们都知道用`sizeWithFont:` 先计算出文字的高度，然后通过计算得出cell的高度，然后赋予`heightForRow:`。  

那在Autolayout时代如何计算cell的高度呢？因为`sizeWithFont:`方法已经不太实用了。其实Autolayout不但更简单，还可以不用写过多的计算代码达到自适应高度。
理论上是可以通过已知的完整的Constraints和view的属性来计算高度的，我们可以通过`systemLayoutSizeFittingSize:`方法来获取计算出来cell的size，我们知道cell的高度需要在tableView的代理方法`tableView:heightForRowAtIndexPath:`中实现的.  

可以参照:http://blog.cnbluebox.com/blog/2015/02/02/autolayout2/  
和http://www.ifun.cc/blog/2014/02/21/dong-tai-ji-suan-uitableviewcellgao-du-xiang-jie/  这两篇文章基本讲的比较全面了.此处不重复.  

核心的API是`systemLayoutSizeFittingSize:`,这个方法可以计算出cell在完整约束下的高度,其中使用这种方式需要注意的是:  
1. UILabel以及UITextField等有`preferredMaxLayoutWidth`属性的VIew需要设置一下,不然计算出来的高度UILable的部分只有一行文字的高度  
2. 对于具有`intrinsicContentSize`的View需要在垂直方向上,将压缩阻力 (Compression Resistance) 和 内容吸附 (Content Hugging)设置为1000,最高级
3. 要保证Cell在垂直方向上设置的Constraints,理论上可以计算出整个Cell的高度,可以降低某个垂直方向约束的优先级,来消除xib中的约束错误提示  


###2.AutoLayout下的动画  
在使用Autolayout时，动画的使用和以前也不同了，以前我们是修改frame，现在我们可以通过修改Constraints, 然后在动画时layoutIfNeeded就行了。

```
//修改约束
...
[UIView animateWithDuration:0.2 animations:^{
    [view layoutIfNeeded];
}];
``` 
Autolayout有时在动画时候会很方便，因为View之间的坐标是相互影响的，在传统frame中，如果改变一个view的frame,那么可能你要更改很多view的frame，才能让页面显得和谐。在Autolayout中可能只需要修改一个Constraint就可以了，在做动画时会很方便。

###3.压缩阻力 (Compression Resistance) 和 内容吸附 (Content Hugging)  

对于`压缩阻力 (Compression Resistance) 和 内容吸附 (Content Hugging)`的介绍,可以参照这里:http://objccn.io/issue-3-5/  
API中对应的是: 

```
- (UILayoutPriority)contentHuggingPriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
- (void)setContentHuggingPriority:(UILayoutPriority)priority forAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
- (UILayoutPriority)contentCompressionResistancePriorityForAxis:(UILayoutConstraintAxis)axis NS_AVAILABLE_IOS(6_0);
- (void)setContentCompressionResistancePriority:(UILayoutPriority)priority forAxis:(UILayout
- 
```