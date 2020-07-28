

# STL

## STL六大部件

> 容器
>
> 分配器(为容器分配内存)
>
> 迭代器
>
> 适配器
>
> 算法
>
> 仿函数

```c++
#include <vector>
#include <algorithm>
#include <functional>
#include <iostream>

using namespace std;
int main(){
	int  a[6] = {19,230,30,40,60,790}
    vector<int,allocator<int>> vi(a,a+6);
    cout << count_if(vi.begin(),vi.end(),not1(bind2nd(less<int>(),40))); //输出大于等于40
    return 0;
}
```

> vector=====>容器
>
> allocator<int>>  =====>分配器
>
> vi.begin(),vi.end()======>迭代器
>
> count_if =====>算法
>
> less=====>仿函数
>
> bind2nd   not1   =====>仿函数适配器



```
lsit<string>  c;

list<string>:: iterator ite;

ite = :: find(c.begin(),c.end(),target);
```



由于auto的出现，可以改写成

```
list<string> c;

auto ite = :: find(c.begin(),c.end(),target);
```

## 容器

### **循序容器**

<u>**array：**  固定大小</u>

**vector：**单端扩充 ——  capacity分配容器大小成2倍增长（会造成空间浪费）   >  size 真正存放的元素个数

**deque：**双端队列 ——一次扩充一个buffer，deque里面的指针，向前扩充指向一个buffer，向后扩充指向另一个buffer

**list：**双向链表   ，节点分离的，不连续

<u>**forward-list：**单向链表</u>

### 关联容器（大量数据）

**set**(红黑树实现)  :key就是value ,value就是key    ======>元素（key ,value）不能重复，唯一标识                                   

**map**（红黑树实现）:每个节点有两个值（一个key,一个value）======>key不能重复，value 可以重复。唯一标识 ——map<long,string>  c; c[i] = string(buf)                  



**Multiset :** key可以重复

**Multimap:**key可以重复     multimap<long,string> c     c.insert(pair<long,string>(i,buff))



**<u>UNordered Containers</u>**（set/Multiset     map/Multimap）

实际上就是hash table  某个元素经过某个函数计算得到的值，决定他应该放在容器的某个位置，这样会存在冲突，某些元素经过函数计算得到相同的值，如何解决呢？得到相同的值就让他放在相同的位置，使用链表将他们关联起来 



*下划线是C++11新增加的*



## OOP面向对象和泛型编程GP

OOP将数据和方法关联在一起 class   list  ，void   sort(),在类的内部，  不能使用全局来排序，list不连续，使用指针只能一个一个往下走，标准库中所提供的sort这个迭代器 需要一定的条件，list不满足，所以list不使用全局sort 排序，需要自己写

GP将数据和方法分开，容器和算法各自闭门造车 ，通过迭代器连通



### 类模板里面



**泛化：**

template <class type>

struct __type_traits{

}



**特化：**

template<> 

struct __type_traits<int>{

}

template<> 

struct __type_traits<double>{

}

**偏特化：**

template<class T,class Alloc=alloc> 

class vector{

}

template<class Alloc> 

class vector<bool,Alloc>{

}

template<class T> 

struct iterator_traits<T*>{

}

template<class T> 

struct iterator_traits<const T*>{

}



**分配器allocators**

分配器调用operator new里面c 的malloc和free分配和释放内存

迭代器的end 是最后一个元素的下一个

**迭代器**

迭代器是容器和算法之间的桥梁，当算法想要寻找某一段的时候，就去询问迭代器，迭代器就回答算法的下面的问题

iterator_category  :iterator移动的性质++  --   +n

difference_type：两个iterator之间的距离

value_type ：iterator指向元素的类型

pointer 

reference

迭代器回答：

```
template<class T,class Ref,class Ptr>

struct __list_iterator{

typedef bidirectional_iterator_tag;

                 iterator_category;

typedef T             value_type;

typedef Ptr           pointer;

typedef Ref           reference;

typedef  ptrdiff_t    difference_type;

}


```

算法提问：

```
template<typename I>

inline void

algorithm( I first,I last){

I:: iterator_category

I:: value_type

I:: pointer

I::reference
I::difference_type

}
```

如果iterator不是一个类呢，只有class才能typedef，比如指针，这样就不能回答问题了

如果传入的是T*   或者const T*，使用**iterator Traits**

```
template<typename I>

algorithm( ){

typename iterator_traits<T>::  value_type  v;

}
```

```


template <class T>

struct iterator_traits{

typedef typename T::value_type value_type;

}
```



```
template <class T>

struct iterator_traits<T*>{

typedef  T value_type;

}
```



```
template <class T>

struct iterator_traits<const T*>{

typedef  T value_type;

}
```



**vector**

三个指针

start

finish

end_of_storage

去别的空间找成两倍增长的空间，每次成长会使用大量的拷贝构造和析构函数



**deque**

deque 是一个分段连续的空间，先存放指向buffer的空间（这个空间实际上是一个vector），迭代器是一个buffer，这个buffer要维持连续的空间，必须有能力跳到下一个缓冲区，迭代器 中的first指向buffer的头，last指向buffer的尾部，node 指向deque空间

迭代器完成假象的连续效果（操作符重载） 

当vector不够时。copy到新vector的中间



**queue**

Sequence C     使用c去调用top、pop、empty等方法。sequence是一个deque

**stack**

Sequence C     使用c去调用top、pop、empty等方法。sequence是一个deque



queue  和stack 都可以选择list 和deque 作为底层结构，默认是deque

queue  和stack不允许遍历 不提供iterator  因为会破坏先进先出、先进后出的规则

stack 可以选择vector 作为底层结构

deque不可以选择vector 作为底层结构    vector没有pop_front

queue  和stack 都不可以选择set和map作为底层结构

stack<string,vector<string>>  c;                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     



set/muliset

 以红黑树为底层结构，也因此元素以key为准有自动排序功能，key就是value，value 就是key

不能使用迭代器修改key 的值

set的key 是独一无二的，multiset key 可以重复

```
set<int>  iset    <== set<int ,  less<int>,alloc> iset    <===template<  int , int , identity<int>, less<int>, alloc> class rb_tree.
```

```
template <class Key, class Compare = less<Key>, class Alloc = alloc>

class set {

public:

​	typedef Key key_type;

​	typedef Key   value_type;

​	typedef compare key_compare;

​	typedef compare value_compare;

private：

​	typedef  rb_tree<key_type,value_type,identity<value_type>,key_cpmpare,Alloc>   rep_type;

​	rep_type  t;

public:

typedef  typename rep_type::const _iterator iterator;

}
```

set 的所有操作都交给红黑树t 去操作，类似于queue 和stack一样交给deque 去操作



map /multimap

 以红黑树为底层结构，也因此元素以key为准有自动排序功能

不能使用迭代器修改key 的值，可以修改data

map的key 是独一无二的，multimap key 可以重复

```
map<int ,string>  imap; <== map<int , string, less<int>,alloc> iset    <===template<  int , pair<const int,string> , select1st<pair<const int ,string>>, less<int>, alloc> class rb_tree.
```



```


template <class Key, class T ,class Compare = less<Key>, class Alloc = alloc>

class map {

public:

	typedef Key key_type;
	typedef T   data_type;
	typedef T   mapped_type;
	typedef pair<const Key,  T>  value_type;
	typedef compare key_compare;

private：

	typedef  rb_tree<key_type,value_type,select1st<value_type>,key_cpmpare,Alloc>   rep_type;//选择第一个值 
	
	rep_type  t;

public:

typedef  typename rep_type::iterator iterator;

}
```

key和data  合成value。      typedef    pair<const Key,  T>  value_type;     key 不可以修改

**map 里面有独特的[] 操作符重载**

c[i] = string(buf);

lower_bound（k）//k在容器中返回第一个出现这个值的位置。如果不存在的话，返回适合安插这个值的位置

在multimap中不能使用[] 只能使用c.insert(pair<long,string>(i,buf));



**hashtable**

1.先申请一定的空间buckets，例如53（质数0-52）。

2.hashcode/buckets取余作为存放的位置。

3.如果某个bucket下面的链表冲突数超过bucket的数，重新申请bucket为97（一般 为原来bucket的二倍附近的质数）。

4.本来连接在bucket的所有元素重新打乱按照相同的方式计算这些元素存储的位置。



