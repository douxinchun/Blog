---
layout: post
title: "Block 声明语法 简记"
date: 2016-11-10 10:55:09 +0800
comments: true
categories: objective_C
---  

#### 本地变量(Local Variable)
> returnType (^blockName)(parameterTypes) = ^returnType(parameters) {...};  

#### 属性(Property)  
> @property (nonatomic, copy, nullability) returnType (^blockName)(parameterTypes);  

#### 方法参数(method parameter)  
> - (void)someMethodThatTakesABlock:(returnType (^nullability)(parameterTypes))blockName;

#### 方法调用的时候的参数
> [someObject someMethodThatTakesABlock:^returnType (parameters) {...}];

#### 重定义(typedef)  
> typedef returnType (^TypeName)(parameterTypes);
> TypeName blockName = ^returnType(parameters) {...};


##参考资料

http://fuckingblocksyntax.com/
