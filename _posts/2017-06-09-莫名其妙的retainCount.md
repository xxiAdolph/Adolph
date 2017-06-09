---
title: 那些年我们遇到de莫名其妙的引用计数（retainCount）
date: 2017-06-09 10:45
---

### NSString 莫名其妙的 retainCount
```
Q: NSString *test = @"test";

A: -1或者UINT_MAX 因为NSString是常量，常量不会释放。
```
```
Q: NSObject *object = test;
   NSLog(@"%@"@([test retainCount]));
   打印出来的会是几呢？

A: 还是-1，因为NSString是常量，怎么引用也是-1

```
