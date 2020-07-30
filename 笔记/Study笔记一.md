

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

***array：***    固定大小

**vector：**  单端扩充 ——  capacity分配容器大小成2倍增长（会造成空间浪费）   >  size 真正存放的元素个数

**deque：**  双端队列 ——一次扩充一个buffer，deque里面的指针，向前扩充指向一个buffer，向后扩充指向另一个buffer

**list：**        双向链表   ，节点分离的，不连续

***forward-list：***         单向链表

### 关联容器（大量数据）

**set**(红黑树实现)  :key就是value ,value就是key    ======>元素（key ,value）不能重复，唯一标识                                   

**map**（红黑树实现）:每个节点有两个值（一个key,一个value）======>key不能重复，value 可以重复。唯一标识 ——map<long,string>  c; c[i] = string(buf)                  



**Multiset :**   key可以重复

**Multimap:**  key可以重复     multimap<long,string> c     c.insert(pair<long,string>(i,buff))



**<u>UNordered Containers</u>**（set/Multiset     map/Multimap）

实际上就是hash table  某个元素经过某个函数计算得到的值，决定他应该放在容器的某个位置，这样会存在冲突，某些元素经过函数计算得到相同的值，如何解决呢？得到相同的值就让他放在相同的位置，使用链表将他们关联起来 



*斜体是C++11新增加的*



### OOP面向对象和泛型编程GP

OOP将数据和方法关联在一起 class   list  ，void   sort(),在类的内部，  不能使用全局来排序，list不连续，使用指针只能一个一个往下走，标准库中所提供的sort这个迭代器 需要一定的条件，list不满足，所以list不使用全局sort 排序，需要自己写

GP将数据和方法分开，容器和算法各自闭门造车 ，通过迭代器连通



### 类模板里面



**泛化：**    用的时候才指定类型

template <class type>

struct __type_traits{

}



**特化： **   特化就是限定死模板实现的具体类型

template<> 

struct __type_traits<int>{

}

template<> 

struct __type_traits<double>{

}

**偏特化： **     模板如果有多个类型，那么就只限定为其中的一部分

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



## **分配器allocators**

分配器调用operator new里面c 的malloc和free分配和释放内存

迭代器的end 是最后一个元素的下一个

## **迭代器**

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

3.如果某个bucket下面的链表冲突数超过bucket的数，重新申请bucket(vector)为97（一般 为原来bucket的二倍附近的质数）。

4.本来连接在bucket的所有元素重新打乱按照相同的方式计算这些元素存储的位置。

```


hashtable<const char* , 
          const char *, 
    hash<const char *>，
identity<const char *>，
                 eqstr,
                 alloc>  ht(50,hash<const char *>()，eqstr());

ht.insert_unique("lilei");
```



C++11  之后将hash_set,hash_multiset     hash_map,hash_multimap   更名为unordered_set,unordered_multiset    unordered_map,unordered_multimap



## 算法

算法看不见容器，独立分开的，各自闭门造车 。他所需要的一切信息都必须从迭代器取得，迭代器由容器提供

iterator_cateegory分类：

input_iterator_tag            output_iterator_tag

farward_iterator_tag       单向链表  （继承于input_iterator_tag  ）

bidirectional_iterator_tag      双向链表      （继承于farward_iterator_tag）

random_access_iterator_tag     随机指针     （继承于bidirectional_iterator_tag  ）

**子类 是一个父类**

迭代器执行某一个运行算法时，先去问iterator_traits（），然后返回一个临时对象来确定是用哪个iterator_cateegory



iterator_cateegory和type_traits 对算法有很重要的影响



**accumulate**

```


template <class InputIterator,
                     class  T>

T  accumulate (InputIterator first,  InputIterator  last , T  init){}
```



```
template <class InputIterator,

                     class  T,

        class BinaryOperation >

T  accumulate (InputIterator first,  InputIterator  last , T  init , BinaryOperation   binary_op ){}
```



**for_each**     对一段内的元素做某一件事

```
template <class InputIterator,
              class  Function>

Function  accumulate (InputIterator first,  InputIterator  last , Function  f){}
```

**replace，replace_if, replace_copy**

```
template <class   ForwardIterator,
                        class  T>

void replace(ForwardIterator  first,

              ForwardIterator  last,

               const T&   old_value,

               const  T&  new_value){}
```



**count**     **count_if**

**find   find_if**

**sort**（list 和forward_list 不存在这个sort函数，不接受跳跃性的指针）

关联式容器不会调用这些类似于全局的函数，因为他们自己有这些函数，而且效率很高



## functor仿函数

只是为算法服务，以仿函数的形式去告诉算法，以我们自己形式去执行算法

class  里面重载 (),这个class创建出来的对象就是函数对象或者仿函数

```
struct myclass{

	bool operator()(int  i , int  j){

	return i<j;

	}

}myobj;     //函数对象

bool myfunc (int i,int j){return i<j;}   //仿函数


sort(myvec.begin(),myvec.end(),myobj);
sort(myvec.begin(),myvec.end(),myfunc);
sort(myvec.begin(),myvec.end(),less<int>());

template<class  T>
struct less: public binary_function<T,T,bool>{
	bool operator()(const T & x,const T & y)const{
	return x<y;
	}
};

```

仿函数的可适配条件：自己写的functor最好继承适当的class，这个类就是适配器（unary_function      binary_function）

类似于算法询问迭代器

```
template<class Operation>

class binder2nd: public unary_function<typename Operation::first_argument_type,                                                                                                typename Operation::result_type>{

protected:

typename Operation::second_argument_type value;    //适配器询问问题，仿函数回答问题下面的三个参数

}

                typedef A1 first_argument_type
                typedef A2 second_argument_type
                typedef R result_type
```



## 适配器

在已经实现好的函数例如（copy）上稍作修改（操作符重载），改成自己适配的形式

类型（）===> 创建临时对象

binder2nd<Operation>(op,arg2_type(x));

```
vector<int>  c  {12,3,4,5,6,77,23}

class  classname {12,4}
```

copy  是开始的位置和结束的位置中间的值copy到另外一个空间的开始位置。必须要指定新的空间的大小不小于迭代的位置大小

reverse_iterator

inserter   中间插入

istream_iterator  std::istream_iterator  <int>   iit(std::cin); //这句话创建之后，已经通过构造函数绑定了输入，可以直接输入了。

ostream_iterator   std::ostream_iterator  <int>   out_it(std::cout, ",");



## 标准库（STL 以外） 

#### 一个万能的hash function

参数可变模板  （n ===> 1  + n-1   ）

```
template <typename... Types>    //接任意个参数类型

inline size_t  hash_val (const  Types &...  args){

	size_t  seed  = 0;

	hash_val(seed,args...);

	return seed;

}

class CustomerHash{

public:

	size_t operator()(const Customer&  c) const{

	return hash_val(c.fname,c.lname,c.no);

	}

}

template <typename T,  typemame... Types>
inline void hash_val (size_t& seed,
  					const T& val,
  					const Types&... args)
{
  	hash_combine(seed,val);
    hash_val(seed,args...);
}

template<typename T>
inline viod hash_val(size_t& seed,const T& val){
	hash_combine(seed,val);
}

template<typename T>
inline void hash_combine(size_t& seed,
                          const T& val)
{
	seed ^= hash<T>()(val) + 0X9e3779b9+(seed<<6)+(seed>>2);
}

unordered_set<Customer,CustomerHash>  set;
set.insert(Customer("lilei","li", 1));
set.insert(Customer("ray","lee", 2));
n = set.bucket_count()  // 篮子数

CustomerHash  ch;
ch(Customer("lilei","li", 1))%n;//通过hash计算之后得到的hashcode 在篮子中的位置
```



#### tuple

任意类型组合

3个参数继承两个参数继承1个参数

```
tuple<int,float,string> t (42,345.5,"lilei");


tuple<int,float,string>    int m_head(42);

tuple<float,string>    float m_head(345.5);

tuple<string>    string m_head("lilei");

template<typename Head,typename... Tail>
class tuple<Head,Tail...>:private tuple<Tail...>{
	typedef tuple<Tail...> inherited;
public:
	tuple(){}
	tuple(Head v,Tail,,, vtaill):m_head(v),inherited(vtail...){}
	typename Head::type head(){
		return m_head;
	}
	inherited& tail(){
		return *this;
	}
protected:
	Head m_head;
}
```



#### type traits

自己实现的类，可以通过type traits知道类里面的很多属性

是否是整型呢？是否有虚析构呢？是否是void呢？是否是类呢？——type traits会回答你

```
//泛化
template<class type>

struct __type_traits{
typedef __false_type  has_trivial_default_constructor;    //默认重要
typedef __false_type  has_trivial_copy_constructor;
typedef __false_type  has_trivial_assignment_opoerator;
typedef __false_type  has_trivial_destructor;
typedef __false_type  is_POD_type;
};

//特化
template<> 
struct __type_traits<int>{
typedef __true_type  has_trivial_default_constructor;    //默认重要
typedef __true_type  has_trivial_copy_constructor;
typedef __true_type  has_trivial_assignment_opoerator;
typedef __true_type  has_trivial_destructor;
typedef __true_type  is_POD_type;
};

//算法询问是否重要
__type_traits<Foo>::has_trivial_destructor
```

classname (classname&&)    //move  constructor

带有指针的才写析构函数

extern  文件之外的可以看见他申明的这个



#### moveable

```
char*  _data; //指针

//copy 构造（深拷贝，两个空间，两个指针）
Mystring(const MyString& str) : _len(str._len){
	++CCtor;
	_init_data(str._data);
}

//move 构造（拷贝一个指针之后把原来的指针置NULL，拷贝后的指针，指向原来的空间。相当于浅拷贝，只是把原来的指针删掉）
MyString(MyString&& str) noexcept: _data(str._data),_len(str._len){
	++MCtor;
	str._len = 0;
	str._data = NULL;
}

M c11(c1);      //耗时
M c12(std::move(c1))      // 不耗时   一定不能再使用 c1 了
```

