title: c++
author: xuezc
tags: []
categories: []
abbrlink: 6f1ca842
description: c++
date: 2023-03-31 10:37:00
---
# 模板
## 可变模版参数
#define EXPAND(x) x这个宏的作用不是很明白...但是必须用...
另外此如果超过了9个数,并不会报错..
```c++
#define EXPAND(x) x

#define PP_NARG(_0, _1,_2,_3,_4,_5,_6,_7,_8,_9,X,...) X

#define PP_NARG_N(...) \
    EXPAND(PP_NARG(0,##__VA_ARGS__,9,8,7,6,5,4,3,2,1,0))
```
## 

# 2
# 杂项
## 获取结构体在堆中的偏移量
e.g.在封装d3d12提及资源时,gpu使用CreateCommittedResource分配好堆,要查询该堆中变量的数据,需要查询index.
<img src="/images/C++_1.png">
```c++
```