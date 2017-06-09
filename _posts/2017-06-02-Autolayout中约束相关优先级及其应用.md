---
title: Autolayout中约束相关优先级及其应用
date: 2017-06-02 17:36
---

#### 关键词:
    约束优先级（Priority）
    Content Hugging Priority
    Content Compression Resistance Priority
    intrinsic content size

#### 理论
* __约束优先级（Priority）:__ 在Autolayout中每个约束都有一个优先级,在xib或storyboard中双击一个约束即可看到它的优先级。优先级的范围是1 ~ 1000。创建一个约束，默认的优先级是最高的1000


* __Content Hugging Priority:__ 该优先级表示一个控件抗被拉伸的优先级。优先级越高，越不容易被拉伸，默认是251。暂时发现UIView, UIButton默认为250。


* __Content Compression Resistance:__ 该优先级和上面那个优先级相对应，表示一个控件抗压缩的优先级。优先级越高，越不容易被压缩，默认是750


* __intrinsic content size:__ 有些控件能通过自己显示的内容计算出需要的Size,这个自动计算出来size就叫该控件的固有内容大小。这个大小是和需要显示的内容相关的。UIButton,UILabel就是具有固有内容大小属性的控件。UIButton可以根据它的title字符串长度和需要显示的image来计算需要的Size,UILabel可以根据它的text来计算。
