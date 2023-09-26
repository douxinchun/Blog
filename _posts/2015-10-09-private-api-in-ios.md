---
layout: post
title: "iOS SDK中的私有API"
date: 2015-10-09 14:37:39 +0800
comments: true
categories: ios
---  

由于是私有API,再键入的时候不会有提示,注意在提交AppStore之前需要全部删除.

1.查看View的层次结构  
> recursiveDescription  

eg:查看UISearchBar的View层级,找到Cancel-Button的位置并修改该按钮的样式  

``` objc
    UISearchBar *searchBarView  = [[UISearchBar alloc] initWithFrame:CGRectMake(0, 0, 375, 44)];
    searchBarView.showsCancelButton = YES;
    //私有API,直接发消息给searchBarView的吧,编译器是不会过的,不信你试试看~~
    NSLog(@"%@",[searchBarView performSelector:@selector(recursiveDescription)]);
    

```  
打印结果如下:  

```
2015-10-09 15:12:20.568 findAward[5625:756750] <UISearchBar: 0x144574710; frame = (0 0; 375 44); text = ''; gestureRecognizers = <NSArray: 0x174247b90>; layer = <CALayer: 0x174430040>>
   | <UIView: 0x17418d750; frame = (0 0; 375 44); clipsToBounds = YES; autoresize = W+H; layer = <CALayer: 0x17442ff80>>
   |    | <UISearchBarBackground: 0x1445a4240; frame = (0 0; 375 44); opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x174431200>>
   |    | <UINavigationButton: 0x14452b250; frame = (0 0; 34 30); opaque = NO; layer = <CALayer: 0x17442e4c0>>
   |    | <UISearchBarTextField: 0x144645db0; frame = (0 0; 0 0); text = ''; clipsToBounds = YES; opaque = NO; layer = <CALayer: 0x1702213a0>>
   |    |    | <_UISearchBarSearchFieldBackgroundView: 0x1445732c0; frame = (0 0; 0 0); opaque = NO; autoresize = W+H; userInteractionEnabled = NO; layer = <CALayer: 0x17442fac0>>  
```  

根据打印的结果,我们可以看出,UISearchBar的二级subview里有一个,`UINavigationButton`随便猜猜,这个也是`UIButton`的子类了.故要获得它并修改他的样式的code如下:  
``` objc  
 for (UIView *view in [[searchBarView.subviews lastObject] subviews]) {
                if ([view isKindOfClass:[UIButton class]]) {
                    UIButton *cancelBtn = (UIButton *)view;
                    [cancelBtn setTitle:@"取消" forState:UIControlStateNormal];
                    [cancelBtn setTitleColor:COLOR_EA4426 forState:UIControlStateNormal];
                    [cancelBtn setTitleColor:COLOR_EA4426 forState:UIControlStateHighlighted];
                }
            }
```
