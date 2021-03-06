# 2020.03.04
* 根据模板创建类和函数的过程叫实例化

* 引用不是对象

* 老式编译器创建vector\<vector\<int>>时要写为"vector\<vector\<int> >"(加空格)

* size_t是无符号类型,可用于表示内存中任意对象的大小,由cstddef头文件定义

***

## 列表初始化
* vector\<string> articles = {"a", "an", "the"}; / vector\<string> articles{"a", "an", "the"}; 用花括号将括起来的值赋值给vector中的元素,这种方式称为列表初始化(C++11),这种方法也可用于结构体,数组等的初始化

* 在使用列表初始化时,花括号中的变量应该与初始化的类型对应,否则将调用其他方法.如:vector\<string> v1{10}; 调用的是vector的构造函数v1(10),即创建10个空的字符串.
***

    // 可以使用C++11的新的遍历方式访问列表等变量
    for(auto &i : v)
        i *= i;

* vector可以用下标来访问已经存在的元素,但是不能创建元素

***

## 迭代器

    // 用迭代器遍历标准库的方法:
    for(auto it = s.begin(); it != s.end(); ++it)
        //do something

* 泛型编程

* const_iterator只能用于读元素,不能用于写元素

* 调用begin,end函数返回时,根据对象是否是常量来确定返回的是iterator还是const_iterator

* cbegin,cend返回const_iterator类型

* 假设it指向vector\<string>对象中的某一元素,则可以用(\*it).empty()来判断是否为空字符串,不可用\*it.empty(),编译器会解释成解引用it中的empty()成员(PS:要切记将it看成类而不是单纯的指针)

* 为了方便调用it指向对象的方法,C++定义了->运算符,因此it->mem等效于(\*it).mem

* 在循环中使用迭代器,要注意不要向容器中添加元素

* 两个迭代器相加减可以得到它们的距离,类型是difference_type的带符号整型数

***

## 数组

* 数组初始化时的长度应该是已知的,维度必须是常量表达式

```
unsigned c = 42; 不是常量表达式 
constexpr unsigned z = 42; 是常量表达式
```

```
int *ptrs[10]; // 含有10个整型指针的数组
int &refs[10] = /*?*/; //错误,不存在引用的数组(因为它不是对象)
int (*ptr)[10] = &arr; //指向一个含有10个整数的数组
int (&ref)[10] = arr; //引用一个含有10个整数的数组
```

* **从右向左,从内向外来确定变量的类型**
    ```
    int *ptrs[10];
    数组->指针->int类型

    int (*ptr)[10];
    (*ptr)是指针,指向int类型的[10]大小的数组
    ```

* 数组和指针关系密切:
    ```
    string num[] = {"one", "two", "three"};
    string *p = &num[0];
    ```

* C++11中可以用begin(数组),end(数组)的形式获得指向头部和尾部的指针,即类似于标准库中迭代器的使用方法

***
## C标准库String函数

* cstring头文件定义

* 以'\0'结尾

```
strlen(p)
strcmp(p1, p2)
strcat(p1, p2)
strcpy(p1, p2)
```

* 如果直接用<,>对比,错误,需要用strcmp

* C++ string标准库中c_str()返回C标准库string
    ```
    string s("Hello World!");
    const char *str = s.c_str();
    ```

***
* 用数组初始化vector对象
    ```
    // 用数组的头指针和尾指针来初始化vector
    int int_arr[] = {0, 1, 2, 3, 4, 5};
    vector<int> ivec(begin(int_arr), end(int_arr));
    ```
***
***

# 2020.03.05

## 多维数组
* 可使用C++的for遍历方式,但要注意父级需要用引用
    ```
    for(const auto &row : ia)
        for(auto col : row)
            cout << col <<endl;
    ```

***

## 左值 右值
* C语言定义: 左值可以位于赋值语句左侧,右值不能

* C++定义: 当一个对象被用作右值时,用的是对象的值(内容); 当对象被用作左值的时候,用的是对象的身份(在内存中的位置)

    * 需要右值的地方可以用左值代替,反之不行

    * 取地址符作用于左值对象时,返回的是指向该**运算对象的指针**,这个指针是一个右值

***

## 求值顺序

```
int i = 0;
cout << i << " " << ++i << endl;
++i需要经过运算才能得到值,上式运算结果是不可预测的,与编译器的处理方式有关,0 1或1 1,等等
 ```

***

* 小整型数据类型,一般会在参与运算的过程中提升为int(或以上)

    ```
    bool b = true;
    bool b2 = -b; // b2是true!
    // 在b2 = -b的过程中,b被提升为int类型,即b = 1,故b2 = -1 != 0,b2为true
    ```

* 取余运算定义

    ```
    // m%(-n) == m%n;(-m)%n==-(m%n)
    21 % 6 --- 3
    21 % 7 --- 0
    -21%-8 --- -5
    21 %-5 --- 1
    ```

* && 和 ||的短路

## 赋值运算符

* 右结合律:从右到左进行赋值

* 防止发生未定义行为

    ```
    while(beg != s.end() && !isspace(*beg))
        *beg = toupper(*beg++);
    赋值表达式的右侧将beg指针移动了,编译器可能对会将表达式理解成以下两种之一(或者其他):
    *beg = toupper(*beg);
    *(beg + 1) = toupper(*beg);
    ```

* 移位运算符

    * 左移<<,向右侧填0

    * 右移>>,根据作用对象是否有符号,是否为负值来确定

## sizeof

```C++
int a = 1;
int b;
sizeof(a);
sizeof(b);
sizeof a;
sizeof b;
// sizeof 不需要实际计算运算对象的值,即使对象无效也可以得到结果
```

* sizeof 作用于string和vector时只返回固定部分的大小,而不是类的大小

* sizeof(ia) / sizeof(*ia)来获得ia的元素数量(对于多为数组,获得的是维度)

***

# 2020.03.06

* 隐式类型转换
    ![隐式类型转换](隐式类型转换.png)

* 无符号类型运算对象处理
    ![无符号类型运算对象处理](无符号类型运算对象处理.png)

* 算术转换实例
    ![算术转换实例](算术转换实例.png)

## **强制转换**
* **尽可能的避免使用强制转换**  
    格式: cast-name<type>(expression);  

    其中cast-name是static_cast,dynamic_cast,const_cast,reinterpret_cast其中一种

* static_cast  
    常规转换,已定义行为,不发出警告

* const_cast
    将const类型变量转换成非常量  
    ![const_cast注意事项](const_cast注意事项.png)

* reinterpret_cast
    强制转换,不安全

***

* switch中的case和其对应的值一起被称为case标签,它必须是整型常量表达式

* 使用switch时,如果没有在case中加括号,且case中含有定义变量并初始化的行为,则出错(注意,如果只是定义变量而不初始化,不认为是错误的).  
    因为case中的变量的作用域超过case本身,运行过程中又是可能跳过的.  
    为了能够让人更好的理解,最好在这种case中加上花括号.

# 2020.03.08

## 可变形参函数

* 当传递的实参类型相同,可以传递一个名为initializer_list的标准库类型

* Initializer_list实例
![Initializer_list实例](Initializer_list实例.png)

***

# PS:回顾 const 指针 引用 数组

## return返回

* 不要返回局部变量的指针或引用类型,其在函数调用结束后将丢失

* 返回数组的指针的声明
![返回数组的指针_声明](返回数组的指针_声明.png)

***

## 函数重载

* 重载函数的要求
![重载函数的要求](重载函数的要求.png)

* 重载被覆盖的问题
![重载_覆盖的问题](重载_覆盖的问题.png)

* 调试信息
![调试信息](调试信息.png)


* 函数匹配
![函数匹配](函数匹配.png)

* 函数指针
![函数指针](函数指针.png)

# 2020.03.09

* 类的初始化过程中,编译器首先编译成员的声明,然后才是成员函数的声明,因此成员函数可以直接调用成员而不用在意其定义的顺序.

* 类的构造,析构,拷贝,赋值都有磨人的合成函数(即就算不定义,也会有默认的行为,无参数),但当定义了含有参数的以上函数后,会使得合成函数不生成,如果还需要使用,则可以在函数后面添加'= default'.

* 类内可以定义成员的初始值

* 用mutable修饰的成员变量在const函数中可以修改值

## **回顾友元**

***

* 友元声明简例
![image](友元声明简例.png)

* 定义类型成员
![image](定义类型成员.png)

* 可以定义友元类,也可以将其他类中的某些方法设置成友元类方法

* 友元声明和作用域
![image](友元声明和作用域.png)

* 成员定义中普通块作用域名字的查找顺序
![image](成员定义中普通块作用域名字的查找顺序.png)

# 2020.03.10

* 构造函数初始值列表
![image](构造函数初始值列表.png)

* 成员的初始化顺序与其在类中定义的顺序一致

* 委托构造函数: 委托构造函数调用所属类的其他构造函数执行自己的初始化过程.

* 默认构造函数的作用
![image](默认构造函数的作用.png)

* 隐式的类类型转换
![image](隐式的类类型转换.png)

    * 可以将构造函数声明为explicit以阻止上面的隐式类类型转换,也就是说,被explicit修饰的构造函数只能直接初始化.

* 聚合类:  
    当类满足以下条件时,称为聚合类  
    * 所有成员是public.
    * 没有定义任何构造函数.
    * 没有类内初始值.
    * 没有基类,也没有虚函数.

    ```C++
    struct Data{
        int val;
        string s;
    }
    // 聚合类的初始化可以用花括号(C++11)
    Data val1 = {0, "Anna"};
    ```

    * 聚合类的初始化任务交给了类的使用者,而且在修改类的成员后需要相应的修改类的初始化语句,这是聚合类主要的缺点

* 字面值常量类

    * constexpr函数的 **参数** 和 **返回值** 必须是字面值类型,类也可以是字面值类型

    * 数据成员都是字面值的类型的聚合类是字面值常量类

    * 字面常量类
    ![image](字面常量类.png)

*** 

## **静态成员**

* 静态成员函数不包含this指针,因此不能定义成const,也不能用this指针来访问非静态成员

* 只需在类的内部声明时使用static关键字即可,在外部定义时不能重复使用static.

* 静态成员变量初始化
![image](静态成员变量初始化.png)

* 静态成员与普通成员的不同
![image](静态成员与普通成员的不同.png)

## IO类

* 三种独立的IO相关头文件
    * iostream
    * fstream
    * sstream
    ![image](IO库类型和头文件.png)

* IO对象没有拷贝和赋值的操作

* IO库条件状态
![image](IO库条件状态.png)

* 管道输出缓冲
    * 系统为了降低io的次数,提高io效率
    * 使用endl,ends,flush函数可以刷新缓冲区,而且在切换cin,cerr,cout之间也会刷新缓冲区
    * cout << unitbuf; 表示所有输出操作后立即刷新缓冲区,即无缓冲,可用cout << nounitbuf; 回到正常模式.
    * 程序运行结束时会刷新缓冲区,但如果程序崩溃,输出缓冲区不会被刷新.

* 关联输入输出流
![关联输入和输出流](关联输入和输出流.png)

***

## 文件输入输出

* fstream常用操作
![fstream常用操作](fstream常用操作.png)

* fstream是iostream子类,因此可以在调用参数为iostream& 的地方传入fstream.

* 如果需要用同一指针打开其他文件,需要先将close()当前的文件

* fstream在离开作用域被销毁的时候回自动关闭.(析构)

* 文件模式
![文件模式](文件模式.png)

* stringstream_string流
![stringstream_string流](stringstream_string流.png)

* 使用ostringstream
![使用ostringstream](使用ostringstream.png)

# 2020.03.11

## 容器

* 顺序容器类型
![顺序容器类型](顺序容器类型.png)

* 确定使用哪种顺序容器
![确定使用哪种顺序容器](确定使用哪种顺序容器.png)

* 容器操作
![容器操作](容器操作.png)

## 迭代器

* 迭代器的范围:左闭右开,[begin,end)

* begin()返回第一个元素位置的迭代器,rbegin()返回最后一个元素位置的迭代器,两个都可以进行++操作,但是移动的方向是相反的(在前面加c表示const).

***

* 容器定义和初始化
![容器定义和初始化](容器定义和初始化.png)

* 标准库array具有固定大小,是模板类型的一部分

    ```
    array<int, 42> // 类型是保存42个int的数组
    array<int> // 错误,未指定大小
    ```

* 标准库array
![标准库array](标准库array.png)

* 赋值和swap
![赋值和swap](赋值和swap.png)

    * [为什么对string调用swap会导致迭代器失效](https://www.cnblogs.com/ll-10/p/9633968.html)

    * [迭代器swap前后是否有效的判断](https://stackoverflow.com/questions/4124989/does-stdvectorswap-invalidate-iterators)

* 容器的关系运算(== < >)是通过元素的关系运算得到的,如果元素没有实现对应的关系运算的功能,则不能进行比较.

* 向顺序容器中添加元素的操作
![向顺序容器中添加元素的操作](向顺序容器中添加元素的操作.png)

* emplace_front,emplace,emplace_back分别对应于push_front,push,push_back.区别在于,push时是将参数中的对象拷贝到容器中,而emplace则是直接用参数在容器中构造元素.

* 每个顺序容器(包括array)都有front成员函数,而除了forward_list之外所有顺序容器都有back成员函数,它们用于返回对应元素的引用.

* at成员函数用于返回指定位置上的元素的引用.

* 顺序容器的删除操作
![顺序容器的删除操作](顺序容器的删除操作.png)

* forward_list特殊操作:因为单链表对当前元素的操作需要改变前面元素的指针,而从当前元素获取前一位元素指针的操作是困难的,因此forward_list定义insert_after,emplace_after,erase_after操作.

# 2020.03.12

* forward_list插入删除操作
![forward_list插入删除操作](forward_list插入删除操作.png)

* resize可以改变容器的大小(array除外),如果变小,删除后面的元素,如果变大,新增元素.

* 容器的改变对迭代器,指针,引用的影响
![容器的改变对迭代器,指针,引用的影响](容器的改变对迭代器,指针,引用的影响.png)

* 当在循环中改变了容器的大小,则要注意实时更新迭代器,如end()等,以免迭代器失效导致错误.

* string replace
    ```
    s.replace(11, 3, "aaaaa");
    // 在下标11的位置后删除3个字符,再插入"aaaaa"5个字符.
    ```

* string搜索操作
![string搜索操作](string搜索操作.png)

* string compare方法
![compare方法](compare方法.png)

* string和数值之间的转换
![string和数值之间的转换](string和数值之间的转换.png)

## 容器适配器

* 容器适配器定义
![容器适配器定义](容器适配器定义.png)

* stack操作
![stack操作](stack操作.png)

* queue操作
![queue操作](queue操作.png)

# 泛型算法

* find(it1, it2, val);  
//在it1,it2两个迭代器之间查找值为val.

* 泛型算法概述
![泛型算法概述](泛型算法概述.png)

# 2020.03.13

* 算法不用执行容器的操作
![算法不用执行容器的操作](算法不用执行容器的操作.png)

## 只读算法(accumulate,equal)
![只读算法(accumulate,equal)](只读算法(accumulate,equal).png)

## 写容器算法

* 写容器算法的范围规定
![写容器算法的范围规定](写容器算法的范围规定.png)

* 算法不检查写操作
![算法不检查写操作](算法不检查写操作.png)

* 插入迭代器(back_inserter)
![back_inserter](back_inserter.png)

* 拷贝算法(copy,replace)
![拷贝算法(copy,replace)](拷贝算法(copy,replace).png)

* sort算法用类型定义的<符号进行进行

* 重排容器元素的算法
![重排容器元素的算法](重排容器元素的算法.png)

* 重新定义sort的排序方式
![向算法传递函数](向算法传递函数.png)

## lambda表达式

* 函数作为谓词可能出现的问题
![谓词可能出现的问题](谓词可能出现的问题.png)
如,希望把长度大于x的string作为true,如果拿普通函数作为一元谓词,需要为每一个x设计一个函数

* lambda简介
![lambda简介](lambda简介.png)

* 向lambda传递参数
![向lambda传递参数](向lambda传递参数.png)

* 使用捕获列表
![使用捕获列表](使用捕获列表.png)

* lambda实例
![lambda实例](lambda实例.png)
![lambda实例(for_each)](lambda实例(for_each).png)

* 完整程序
![完整程序](完整程序.png)

* lambda实现细节
![lambda实现细节](lambda实现细节.png)

* 值捕获和引用捕获
![值捕获和引用捕获](值捕获和引用捕获.png)

* 隐式捕获
![隐式捕获](隐式捕获.png)

* 捕获方式总结列表
![lambda捕获列表](lambda捕获列表.png)

* 指定lambda返回类型
![指定lambda返回类型](指定lambda返回类型.png)

* 对于没有捕获列表的lambda函数,实际上是可以用普通函数来代替作为谓语的,具体可根据实际情况决定

## 标准库bind函数

* 举例的函数
![check_size函数](check_size函数.png)

* bind函数及实例.png
![bind函数及实例](bind函数及实例.png)

* 命名空间placeholders
![命名空间placeholders](命名空间placeholders.png)

* bind引用参数
![bind引用参数](bind引用参数.png)

## 迭代器
* 迭代器种类
![迭代器种类](迭代器种类.png)

* 插入迭代器
![插入迭代器](插入迭代器.png)

* iostream迭代器
    * istream_iterator操作
    ![istream_iterator操作](istream_iterator操作.png)
    ![istream_iterator操作表](istream_iterator操作表.png)

    * istream_iterator懒惰求值:在读取数据时迭代器并不会立即读取,直到我们使用迭代器时才真正读取

    * ostream_iterator操作
    ![ostream_iterator操作](ostream_iterator操作.png)

    * 使用流迭代器处理类类型
    ![使用流迭代器处理类类型](使用流迭代器处理类类型.png)


# 2020.03.15

## 泛型算法结构

* 按迭代器读写,随机访问能力分类
![按迭代器读写,随机访问能力分类](按迭代器读写,随机访问能力分类.png)

### 类迭代器

* 迭代器区别及兼容性
![迭代器区别及兼容性](迭代器区别及兼容性.png)

* 迭代器类别详解
![迭代器类别详解](迭代器类别详解.png)

### 算法形参模式

![算法形参模式](算法形参模式.png)

### 算法命名规范

![算法命名规范](算法命名规范.png)

### 特定容器算法

![特定容器算法](特定容器算法.png)

# 关联容器

![关联容器分类](关联容器分类.png)

## 使用关联容器



## 关联容器概述

* 关联容器支持普通容器的操作,但不支持顺序容器的位置相关的操作,同时也不支持构造函数或插入操作中参数为数字和元素组合的操作

* 有序容器要求关键字必须定义元素比较的方法,<,在集合类型中,关键字类型也就是元素类型

* 使用关键字类型的比较函数
![使用关键字类型的比较函数](使用关键字类型的比较函数.png)

* pair操作
![pair操作](pair操作.png)

## 关联容器操作

* 关键容器额外的类型别名
![关键容器额外的类型别名](关键容器额外的类型别名.png)

* 关联容器迭代器不允许修改关键字的值

* 关联容器insert操作
![关联容器insert操作](关联容器insert操作.png)

* 检测insert的返回值
![检测insert的返回值](检测insert的返回值.png)

* 向multiset或multimap添加元素
![向multiset或multimap添加元素](向multiset或multimap添加元素.png)

* 删除元素调用erase函数,函数的返回值是删除的元素的数量.

* 非const的map可以用下标操作来进行赋值,修改值.当要判断某个key是否存在时,可以使用 c.at(k)的方法,未找到时抛出out_of_range异常.

* 关联容器中查找元素的操作
![关联容器中查找元素的操作](关联容器中查找元素的操作.png)

* 在multimap或multiset中查找元素  
**相同关键字的元素会存放在相邻的位置上**
![在multimap或multiset中查找元素](在multimap或multiset中查找元素.png)

* equal_range返回的pair中的类型是迭代器类型.

## 无序容器

* 使用哈希函数建立关联,在关键字没有顺序关系时使用

* 管理桶
![管理桶](管理桶.png)

* 无序容器对关键字类型的要求
![无序容器对关键字类型的要求](无序容器对关键字类型的要求.png)

# 动态内存

* 静态内存和动态内存
![静态内存和动态内存](静态内存和动态内存.png)

## 动态内存与智能指针

* shared_ptr允许多个指针指向同一个对象,unique_ptr独占所指向的对象,weak_ptr表示弱引用,指向shared_ptr所管理的对象. 三种类型都定义在memory头文件中.

### shared_ptr类

* shared_ptr操作
![shared_ptr操作](shared_ptr操作.png)

* make_shared函数
![make_shared函数](make_shared函数.png)

* shared_ptr拷贝和赋值
![shared_ptr拷贝和赋值](shared_ptr拷贝和赋值.png)

* shared_ptr自动释放相关联的内存
![shared_ptr自动释放相关联的内存](shared_ptr自动释放相关联的内存.png)

* shared_ptr实例
![shared_ptr实例](shared_ptr实例.png)

* new 动态分配内存,可以用const修饰

* delete 删除动态分配的内存,delete后面跟着的是指针.

* delete之后要空置指针,否则即出现野指针

* shared_ptr与new结合使用
![shared_ptr与new结合使用](shared_ptr与new结合使用.png)

* 不要混合使用普通指针和智能指针.

* 也不要用get初始化另一个智能指针或智能指针赋值.

* 其他shared_ptr操作
![其他shared_ptr操作](其他shared_ptr操作.png)

### 智能指针和异常

* 用智能指针自调用析构函数的特性来降低内存泄漏异常发生的概率.

* 智能指针和哑类
![智能指针和哑类](智能指针和哑类.png)

* 智能指针陷阱
![智能指针陷阱](智能指针陷阱.png)

### unique_ptr

* 一个unique_ptr独占所指向的对象,也就是说在unique_ptr被销毁时,其对应的内存一定会被释放.

* unique_ptr不支持拷贝,不支持赋值(即用unique_ptr对象赋值)

* unique_ptr操作
![unique_ptr操作](unique_ptr操作.png)

* 切换指针的所用权
![切换指针的所用权](切换指针的所用权.png)

* unique_ptr赋值例外,允许将局部的unique_ptr作为返回值(返回时发生赋值操作)
![unique_ptr赋值例外](unique_ptr赋值例外.png)

* 向unique_ptr传递删除器
![向unique_ptr传递删除器](向unique_ptr传递删除器.png)

### weak_ptr

* 弱指针不控制所指向对象的生存期,它指向shared_ptr管理的对象,weak_ptr绑定到shared_ptr上不会改变其引用计数.在shared_ptr的引用计数为0时,不管有没有弱指针,都会进行内存释放.

* weak_ptr操作
![weak_ptr操作](weak_ptr操作.png)

## 动态数组

* new T[n]创建动态数组,其中的n可以是0,但是返回值不能解引用.(不能定义长度为0的数组,但创建动态数组是合法的)

* delete[] p 用于删除动态数组

* 智能指针和动态数组
![智能指针和动态数组](智能指针和动态数组.png)

### allocator类

* allocator类及其算法
![allocator类及其算法](allocator类及其算法.png)

* allocator操作详解
![allocator操作详解](allocator操作详解.png)

## 使用标准库:文本查询程序(Program)

* page 430

# 拷贝控制

* 五种特殊的成员函数(拷贝控制操作):  
拷贝构造函数  
拷贝赋值函数  
移动构造函数  
移动赋值运算符  
析构函数  

## 拷贝,赋值与销毁

### 拷贝构造函数

* 第一个参数是自身类类型的引用的构造函数称为拷贝构造函数  
    ```
    Foo(const Foo&)
    ```

* 合成拷贝构造函数(默认的拷贝构造函数),与合成构造函数不同,即使定义了其他的构造函数,编译器也会合成一个拷贝构造函数

* 合成拷贝构造函数创建对象过程
![合成拷贝构造函数创建对象过程](合成拷贝构造函数创建对象过程.png)

* 拷贝初始化
![拷贝初始化](拷贝初始化.png)

* 参数和返回值
![参数和返回值](参数和返回值.png)

### 拷贝赋值运算符

* 重载赋值运算符
![重载赋值运算符](重载赋值运算符.png)

* 合成拷贝赋值运算符

### 析构函数

* 析构函数不接受参数,因此不能重载

* 析构函数首先执行函数体,然后销毁成员,成员按初始化的顺序逆序销毁

* 调用析构函数的时机
![调用析构函数的时机](调用析构函数的时机.png)

* 合成析构函数

### 三/五法则

* 需要析构函数的类也需要拷贝和赋值操作
![需要析构函数的类也需要拷贝和赋值操作](需要析构函数的类也需要拷贝和赋值操作.png)

* 需要拷贝操作的类也需要赋值操作,反之亦然
![需要拷贝操作的类也需要赋值操作,反之亦然](需要拷贝操作的类也需要赋值操作,反之亦然.png)

### 使用=default

* =default用于显示的要求编译器生成合成版本的函数.

### 阻止拷贝

* 部分情况下,类的拷贝操作是没有意义的,如iostream不允许拷贝.  
可以用=delete来表示删除的函数

* 析构函数不能是删除的成员(实际上删除了析构函数的类型,我们不能定义这种类型或成员,但可以动态分配这种类型的对象,但不能释放)  
    ![删除析构函数的类型](删除析构函数的类型.png)

* 合成拷贝控制成员可能是删除的情况
![合成拷贝控制成员可能是删除的情况](合成拷贝控制成员可能是删除的情况.png)

* 通过将拷贝控制函数定义为private来阻止拷贝,不定义它们,使友元也调用它们

## 拷贝控制和资源管理

* 要根据类的成员的行为是一个值还是一个指针来定义合适的拷贝控制函数

### 行为像值的类

* 类值拷贝赋值运算符
![类值拷贝赋值运算符](类值拷贝赋值运算符.png)

### 行为像指针的类

* 引用计数
![引用计数](引用计数.png)

* 定义使用引用计数的类
![定义使用引用计数的类](定义使用引用计数的类.png)

* 修改引用计数
![修改引用计数](修改引用计数.png)

## 交换操作

* swap 函数,用于重排元素顺序的算法,在交换两个元素是会调用

* 自定义swap函数
![自定义swap函数](自定义swap函数.png)

* 在赋值运算符中使用swap
![在赋值运算符中使用swap](在赋值运算符中使用swap.png)

## 拷贝控制示例

* page 486 Message类与Folder类,表示信息和文件目录的关系

## 动态内存管理类

* page 490 StrVec类,创建一个存储string的自定义vector

* 移动构造函数  
消除拷贝操作带来的性能消耗
![移动构造函数](移动构造函数.png)

## 对象移动

### 右值引用
![右值引用](右值引用.png)

### 移动构造函数和移动赋值运算符

* 创建移动构造函数
![创建移动构造函数](.png)

* 移动操作,标准库容器和异常
![移动操作,标准库容器和异常](移动操作,标准库容器和异常.png)

* 移动赋值运算符
![移动赋值运算符](移动赋值运算符.png)

* 移后对象必须可析构
![移后对象必须可析构](移后对象必须可析构.png)

* 合成移动操作
![合成移动操作](合成移动操作.png)

* 如果一个类定义了移动构造函数和拷贝构造函数,则右值移动,左值拷贝.如果没有定义移动构造函数,则右值也被拷贝.

* 更新三五法则
![更新三五法则](更新三五法则.png)

* 移动迭代器
![移动迭代器](移动迭代器.png)

### 右值引用和成员函数
* 移动重载函数接收T&&(右值引用),拷贝重载函数接收const T&(const左值引用)

* 引用限定符(修饰this指针)
    ```
    Foo &Operator=(const Foo&) &; // 只能向可修改的左值赋值
    Foo Sorted() &&; // 对象为右值
    ```
* 重载和引用函数
![重载和引用函数](重载和引用函数.png)

# 重载运算与类型转换

## 基本概念 

* 重载运算符基本概念
![重载运算符基本概念](重载运算符基本概念.png)

* 调用运算符函数
```C++
data1 + data2;
//等效于
operator+(data1, data2);
```

* 不应该被重载的运算符
![不应该被重载的运算符](不应该被重载的运算符.png)

* 使用与内置类型一致的含义

* 赋值和复合赋值运算符

* 选择作为成员或非成员
![选择作为成员或非成员](选择作为成员或非成员.png)

## 输入和输出运算符

### 重载输出运算符
![重载输出运算符](重载输出运算符.png)

### 重载输入运算符
![重载输入运算符](重载输入运算符.png)

## 算术和关系运算符

* 算数和关系运算符
![算数和关系运算符](算数和关系运算符.png)

### 相等运算符
![相等运算符](相等运算符.png)

### 关系运算符
![关系运算符](关系运算符.png)

### 赋值运算符
![赋值运算符](赋值运算符.png)

## 下标运算符
![下标运算符](下标运算符.png)

## 递增和递减运算符
![递增和递减运算符](递增和递减运算符.png)

## 成员访问运算符
![成员访问运算符](成员访问运算符.png)

## 函数调用运算符
![函数调用运算符](函数调用运算符.png)

* lambda是函数对象

* 标准库定义的函数对象
![标准库函数对象](标准库函数对象.png)

### 可调用对象与function
![可调用对象与function](可调用对象与function.png)

## 重载,类型转换与运算符

### 类型转换运算符
![类型转换运算符](类型转换运算符.png)

* 避免过度使用类型转换函数

* 类型转换运算符可能产生意外结果

* explicit 可以防止隐式的类型转换

### 避免有二义性的类型转换

* 无法精确匹配的情况下出现,调用重载函数时出现二义性的问题


# 面向对象设计

* OOP三个基本概念:数据抽象,继承和动态绑定

## OOP概述

* 继承  
  基类 派生类  
  虚函数

* 动态绑定

## 定义基类和派生类

### 定义基类
![定义基类](定义基类.png)

### 定义派生类

* 类派生列表
![类派生列表](类派生列表.png)

* 派生类中的虚函数  
派生类可以覆盖他继承的虚函数,或者直接继承基类中的版本

* 派生类对象及派生类向基类的类型转换  
派生类在内存中存放的方式可以认为是先存放基类的内容,因此基类指针可以对指向派生类而又能正确调用基类函数

* 派生类构造函数  
派生类需要使用基类的构造函数初始化基类部分,再构造自己

* 派生类使用基类成员  
派生类可以访问基类到的公有成员和受保护成员  
![遵循基类的接口](遵循基类的接口.png)

* 继承与静态成员  
如果基类定义一个静态成员,则整个继承体系只存在该成员的唯一定义

* 派生类的声明  
派生类的声明和其他类差别不大,声明时不需要包含它的派生列表

* 被作为基类的类必须是已经定义了的

* final关键字可以防止类被继承

### 类型转换与继承
* 可以将基类的指针或引用绑定到派生类对象上

* 静态类型和动态类型  
静态类型是编译时已知的类型,动态类型直到运行时才可知

* 不存在基类向派生类的隐式类型转换

* 派生类向基类的自动类型转换只对指针或引用类型有效,在派生类类型与基类类型之间不存在这样的转换(即在对象之间不存在类型转换)  
当我们把派生类作为参数传递给基类的时候,实际运行的构造函数时基类中定义的那个,反之同理.  
![存在继承关系的类型之间的转换规则](存在继承关系的类型之间的转换规则.png)

## 虚函数

![C++的多态性](C++的多态性.png)

* 派生类中的虚函数
![派生类中的虚函数](派生类中的虚函数.png)

* final和override说明符  
override说明定义的函数用于覆盖基类的虚函数,这是为了防止编程中没有将派生类虚函数和基类虚函数参数完全匹配,导致难以察觉.  
final 修饰的函数不允许派生类覆盖

* 回避虚函数的机制  
显示的在调用的函数前添加作用域,可以调用指定类的虚函数

## 抽象基类

* 纯虚函数  
virtual int Foo() = 0;  
在末尾加上"=0"
纯虚函数表示当前类不能定义该函数,只能在派生类中定义  
含有纯虚函数的类是抽象基类

* 重构
![重构](重构.png)

## 访问控制与继承
![访问控制](访问控制.png)

* 类的设计与受保护的成员
![类的设计与受保护的成员](类的设计与受保护的成员.png)

* 友元关系不能传递,同样也不能继承

* 可以使用using来改变基类成员的访问级别

* struct默认public, class默认private

## 继承中的类作用域

* 在编译时进行名字查找,不能用基类指针来调用派生类的特有的函数,即使基类指针实际指向的确实是派生类对象

* 名字冲突与继承  
派生类可以定义相同名字的成员,此时基类中的同名成员将被隐藏,可以使用指定作用域的方法显示的调用被隐藏的基类成员

* 名字查找先于类型检查  
即使派生类定义的成员函数在参数类型上和基类的不同,但仍会将基类成员覆盖

* 虚函数与作用域
![虚函数与作用域](虚函数与作用域.png)

## 构造函数与拷贝控制

### 虚析构函数

* 当我们用delete删除掉基类指针的对象时,实际上应该需要调用对应动态类型的对象的析构函数,所以将基类中的析构函数定义成虚函数以确保执行正确的析构函数版本

* 虚析构函数将组织合成移动操作

### 合成拷贝控制与继承

* 构造函数调用顺序:基类到派生类  
析构函数调用顺序:派生类到基类

* 派生类中删除的拷贝控制与基类的关系
![派生类中删除的拷贝控制与基类的关系](派生类中删除的拷贝控制与基类的关系.png)

* 移动操作与继承
![移动操作与继承](移动操作与继承.png)

### 派生类的拷贝控制成员

* 定义派生类的拷贝或移动构造函数
![定义派生类的拷贝或移动构造函数](定义派生类的拷贝或移动构造函数.png)

* 派生类赋值运算符同上,显式的调用来为基类部分赋值

* 在构造函数和析构函数中调用虚函数
![在构造函数和析构函数中调用虚函数](在构造函数和析构函数中调用虚函数.png)

### 继承的构造函数

* 继承的构造函数简述
![继承的构造函数简述](继承的构造函数简述.png)

* 继承的构造函数的特点
![继承的构造函数的特点](继承的构造函数的特点.png)

## 容器与继承

* 容器中放置指针而非对象,放置基类对象的容器会将派生类的部分删除,导致意外的结果

## 文本查询程序再探

* page 588 (**import**)

# 模板与泛型编程

## 定义模板

### 函数模板
```C++
template<typename T>
int compare(const T &v1, const T &v2){...}
```

* 实例化函数模板

* 模板类型参数

* 非类型模板参数
![非类型模板参数](非类型模板参数.png)

* 可以用inline和constexpr修饰函数模板

* 编写类型无关的代码
![编写类型无关的代码](编写类型无关的代码.png)

* 模板编译  
当编译器遇到模板定义时,并不生成代码,只有在实例化之后才会生成代码

### 类模板
* 对于一个实例化的类模板,其成员只有在使用时才被实例化

### 模板参数
* 使用类的类型成员
![使用类的类型成员](使用类的类型成员.png)

### 成员模板


## 模板实参推断


## 重载与模板


## 可变参数模板


## 模板特例化