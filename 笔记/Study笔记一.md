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

**vector：**单端扩充 ——  capacity分配容器大小成2的指数增长（会造成空间浪费）   >  size 真正存放的元素个数

**deque：**双端队列 ——一次扩充一个buffer，deque里面的指针，向前扩充指向一个buffer，向后扩充指向另一个buffer

**list：**双向链表

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

OOP将数据和方法关联在一起 class   list  ，void   sort(),在类的内部，  不能使用全局来排序，list不连续，使用指针

GP将数据和方法分开，容器和算法各自闭门造车 ，通过迭代器连通

