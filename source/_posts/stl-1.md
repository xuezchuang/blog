title: stl
author: xuezc
tags: []
description: 对标准库的深入学习
categories: []
abbrlink: a6a19e9a
date: 2023-04-02 10:34:00
---
# 深入stl Iterator
## 迭代器的数据结构
容器本身指向_Container_proxy

```c++
struct _Container_base12;
struct _Iterator_base12;
#容器指针
struct _Container_proxy
{	
    #store head of iterator chain and back pointer
    const _Container_base12 *_Mycont;
    _Iterator_base12 *_Myfirstiter;
    
};
#容器结构体
struct _Iterator_base12
{       
    _Container_proxy *_Myproxy;
    _Iterator_base12 *_Mynextiter;
}
#容器
struct _Container_base12
{       
    _Container_proxy *_Myproxy;
}
```


# Alloc的初始化,全部以Deque容器为例
Alloc的初始化会初始化容器_Container_base12


## _Deque_alloc分配符
```c++
template<class _Val_types>
struct _Deque_val: public _Container_base12
{
    typedef typename _Val_types::_Mapptr _Mapptr;
    
    _Mapptr _Map;       // pointer to array of pointers to blocks
    size_type _Mapsize;	// size of map array, zero or 2^N
    size_type _Myoff;	// offset of initial element
    size_type _Mysize;	// current length of sequence

    
};
#_Deque_alloc的分配符
template<bool _Al_has_storage,class _Alloc_types>
class _Deque_alloc : public _Deque_val<typename _Alloc_types::_Val_types>
{
}
#_Deque_alloc的模板特例化分配符
template<false,class _Alloc_types>
class _Deque_alloc : public _Deque_val<typename _Alloc_types::_Val_types>
{
}
struct _Container_proxy
{	
    #store head of iterator chain and back pointer
    const _Container_base12 *_Mycont;
    _Iterator_base12 *_Myfirstiter;
    
};
```

## allocator分配符结构
_Allocator_base
```c++
template<class _Ty>
struct _Allocator_base
{	
    typedef _Ty value_type;
};
#模板特化
template<class _Ty>
struct _Allocator_base<const _Ty>
{	
    typedef _Ty value_type;
};
```
allocator
```c++
template<class _Ty>
class allocator: public _Allocator_base<_Ty>
{	
    typedef _Ty value_type;
};
```

# 容器的实现
不指定Alloc的情况下,基类都是_Deque_alloc<false,class _Alloc_types>

```c++
template<class _Ty,class _Alloc = allocator<_Ty> >
class allocator: 
    public _Deque_alloc<<!is_empty<_Alloc>::value,_Deque_base_types<_Ty, _Alloc>>
{	
    typedef _Ty value_type;
};
```

```c++
template<class _Ty>
struct _Deque_simple_types
{	
    typedef _Alloc0 _Alloc;
    typedef _Deque_base_types<_Ty, _Alloc> _Myt;
    #如果是基础类型,返回_Deque_simple_types<>,否则返回自定义结构体    
    typedef typename _If<_Is_simple_alloc<_Alty>::value,
		_Deque_simple_types<typename _Alty::value_type>,
		_Deque_iter_types<typename _Alty::value_type,
			typename _Alty::size_type,
			typename _Alty::difference_type,
			typename _Alty::pointer,
			typename _Alty::const_pointer,
			typename _Alty::reference,
			typename _Alty::const_reference,
			_Mapptr> >::type
		_Val_types;
};
```
_Deque_base_types
```c++
template<class _Ty,class _Alloc0>
struct _Deque_base_types    
{	
    typedef _Alloc0 _Alloc;
    typedef _Deque_base_types<_Ty, _Alloc> _Myt;
    #如果是基础类型,返回_Deque_simple_types<>,否则返回自定义结构体    
    typedef typename _If<_Is_simple_alloc<_Alty>::value,
		_Deque_simple_types<typename _Alty::value_type>,
		_Deque_iter_types<typename _Alty::value_type,
			typename _Alty::size_type,
			typename _Alty::difference_type,
			typename _Alty::pointer,
			typename _Alty::const_pointer,
			typename _Alty::reference,
			typename _Alty::const_reference,
			_Mapptr> >::type
		_Val_types;
};
```













































































