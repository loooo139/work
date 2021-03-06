# c++ 常见面试题

1. ## **C和C++的区别是什么？**
   C是面向过程的语言，C++是在C语言的基础上开发的一种面向对象编程语言，应用广泛。
   C中函数不能进行重载，C++函数可以重载
   C++在C的基础上增添类，C是一个结构化语言，它的重点在于算法和数据结构。C程序的设计首要考虑的是如何通过一个过程，对输入（或环境条件）进行运算处理得到输出（或实现过程（事务）控制），而对于C++，首要考虑的是如何构造一个对象模型，让这个模型能够契合与之对应的问题域，这样就可以通过获取对象的状态信息得到输出或实现过程（事务）控制。

   C++中struct和class除了默认访问权限外，别的功能几乎都相同。

2. ## **关键字 static const extern 作用：**

   **static**：

   1. 函数体内static变量的作用范围为该函数体，该变量的内存只分配一次，因此下次调用时仍然维持上次的值
   2. 在模块内部的static全局变量和函数可以被模块内的函数访问，但不能被模块外的其他函数访问。
   3. 在类中的static成员变量属于整个类所有，对类的所有对象只有一份拷贝。
   4. 在类中的static成员函数属于整个类所有，这个函数不接受this指针，因此只能访问类的static成员变量

   **const**：

   1. 阻止一个变量被改变
   2. 声明常量指针和指针常量
   3. const 修饰形参，表明它是一个输入参数，在函数内部不能被改变。
   4. 对于类的成员函数，若指定其为const类型，则表明其是一个常函数，不能修改类的成员变量
   5. 对于类的成员函数，有时候必须指定其返回值为const类型，以使得其返回值不为”左值”

   **extern**:

   1. extern可以置于变量或者函数前，以标示变量或者函数的定义在别的文件中，提示编译器遇到此变量或者函数在别的模块中寻找定义
   
   2. extern "C"的作用是让 C++ 编译器将extern "C"声明的代码当作 C 语言代码处理，可以避免 C++ 因符号修饰导致代码不能和C语言库中的符号进行链接。
   
3. ## **sizeof和strlen的区别**

   1. sizeof是运算符，而strlen是函数

   2. sizeof的用法是sizeof（参数），这个参数可以是指针，数组，类型，对象，甚至是函数，而其值在编译的时候就计算好了，而strlen的参数必须是字符型指针（char*），其值必须在函数运行的时候才能计算出来。

   3. sizeof的功能是获得保证能容纳实现的简历的最大对象的字节大小，而strlen是返回字符串长度，切记这里的字符串长度是包含结束符号的。

   4. 当数组作为参数传递给函数的时候，传的是指针，而不是数组，传递数组的首地址。

      ```cpp
      char str[20] = "0123456789";
      int a = strlen(str);  //10
      int b = sizeof(str);//20
      ```

4. ## 指针和引用的区别

   1. 指针是一个变量，只不过这个变量储存是一个地址，指向内存的一个存储单元；而引用跟原来的变量本质上是同一个东西，只不过是原变量的一个别名而已。

   2. 指针可以有多级，而引用只有一级

   3. 指针的值可以为空，但是引用的值不能为空，并且引用在定义的时候必须初始化

   4. 指针的值在初始化后可以改变，即指向其他的存储单元，而引用在初始化后就不会改变。

   5. "sizeof引用"得到的是所指向的变量(对象)的大小，而"sizeof指针"得到的是指针本身的大小。

   6. 作为参数传递的时候，两者又本质的不同：指针传参本质是值传递，被调函数的形参作为局部变量在栈中开辟内存一存放由主调函数放进来的实参值，从而形实参的一个副本。而引用传递时候，被调函数对形参的任何操作都会通过一个间接寻址的方式影响主调函数中的实参变量。

      如果想通过指针参数传递来改变主调函数中的相关变量，可以使用指针的指针。

5. ## 指针数组，数组指针，函数指针

   1. 指针数组：首先它是一个数组，数组的元素都是指针，数组占多少个字节由数组本身的大小决定，每一个元素都是一个指针，在32位系统中任何类型的指针永远都是4个字节

   2. 数组指针：首先它是一个指针，指向一个数组。在32位系统下任何类型的指针永远都是占4个字节，至于他指向的数组占多少字节，不知道，具体要看数组的大小。

      ```cpp
          int arr[] ={1,2,3,4,5};
          int *ptr =(int *)(&arr+1);  //2  5
          int *ptr =(int *)(arr+1);   //2  1
          cout<<*(arr+1)<<" "<<*(ptr-1)<<endl;
      //数组名arr可以作为数组的首地址，而&a是数组的指针。
      //arr和&arr指向的是同一块地址，但他们+1后的效果不同，arr+1是一个元素的内存大小（增加4）
      //而&arr+1增加的是整个数组的内存
      ```

      数组指针：

      ```cpp
          int a[2][3] = {{1,2,3},{4,5,6}};
          int (*p)[3];
          p = a;
          p++;
          cout<<**p<<endl;  //4  the second rank
      ```

6. ## c++内存布局

   1. 栈区（stack）：由编译器自动分配释放，存放函数的参数值，局部变量值，其操作方法类似数据结构中的栈
   2. 堆区（heap）：一般由程序员分配释放，与数据结构中的heap无关，分配方式类似链表。
   3. 全局/静态（static）：全局变量和静态变量的存储是放在一起的，在程序编译时分配。
   4. 字符串常量区：存放常量字符串
   5. 程序代码区：存放函数体（类的成员函数，全局函数）的二进制代码。

   ```cpp
   int a=0; //全局初始化区
   char *p1; //全局未初始化区
   void main()
   {
   	int b; //栈
   	char s[]="bb"; //栈
   	char *p2; //栈
   	char *p3="123"; //其中，“123\0”常量区，p3在栈区
   	static int c=0; //全局区
   	p1=(char*)malloc(10); //10个字节区域在堆区
   	strcpy(p1,"123"); //"123\0"在常量区，编译器 可能 会优化为和p3的指向同一块区域
   ｝
   
   ```

   ### c++ 内存分配的三种方式：

   1. 从静态存储区域分配。内存在程序编译的时候就已经分配完毕，这个内存在程序的整个运行期间都存在。例如：全局变量，static变量。
   2. 在栈上创建，在执行函数的是，函数内的局部变量的存储单元可以在栈上创建，函数执行完毕后这些存储单元会自动释放。
   3. 在堆上分配，亦称动态内存分配。程序在运行的时候用malloc或者new申请任意多的内存，程序员负责在何时用free或delete释放内存。动态内存的生存期由程序员决定，使用非常灵活。但如果在堆上分配了空间，就有责任回收他，否则运行的程序会出现内存泄漏。另外频繁的分配和释放不同大小的堆空间会产生堆内碎块。

7. ## 堆和栈的区别

   1. 申请方式上：

      1. stack：由系统分配，例如在函数中声明一个局部变量。

      2. heap：由程序员分配，并指明大小，并负责回收。

         ```cpp
         如p1 = (char *)malloc(10); 
         在C++中用new运算符 
         如p2 = (char *)malloc(10); 
         但是注意p1、p2本身是在栈中的。 
         ```

   2. 申请后系统的相应：

      1. stack：只要栈的剩余空间大与所申请的空间，系统将为程序提供内存，否则提示栈溢出。
      2. heap：操作系统中有一个记录空闲内存地址的链表，当系统收到程序的申请之后就会遍历该链表，找到第一个空间大与所申请空间的堆节点，然后将该节点从链表中删去，且将该节点的空间分配给程序。另外对于大多数的系统，会在这块内存空间的首地址处记录下本次分配的大小。这样代码中delete语句才能正确的释放本内存空间。另外，由于找到的堆节点大小不一定正好等于申请的大小，系统会自动的将多余的那一部分在重新放回空闲链表。

   3. 申请大小的限制及生长方向：

      栈：在Windows下,栈是向低地址扩展的数据结构，是一块连续的内存的区域。这句话的意思是栈顶的地址和栈的最大容量是系统预先规定好的，在WINDOWS下，栈的大小是2M（也可能是1M，它是一个编译时就确定的常数），如果申请的空间超过栈的剩余空间时，将提示overflow。因此，能从栈获得的空间较小 。

      堆：堆是向高地址扩展的数据结构，是不连续的内存区域。这是由于系统是用链表来存储的空闲内存地址的，自然是不连续的，而链表的遍历方向是由低地址向高地址。堆的大小受限于计算机系统中有效的虚拟内存。由此可见，堆获得的空间比较灵活，也比较大。

   4. 申请效率的比较：
      栈由系统自动分配，速度较快。但程序员是无法控制的。
      堆是由new分配的内存，一般速度比较慢，而且容易产生内存碎片,不过用起来最方便.

   5. 堆和栈中的存储内容：

      栈：在函数调用时，第一个进栈的是主函数中后的下一条指令（函数调用语句的下一条可执行语句）的地址，然后是函数的各个参数，在大多数的C编译器中，参数是由右往左入栈的，然后是函数中的局部变量。注意静态变量是不入栈的。
      当本次函数调用结束后，局部变量先出栈，然后是参数，最后栈顶指针指向最开始存的地址，也就是主函数中的下一条指令，程序由该点继续运行。

      堆：一般是在堆的头部用一个字节存放堆的大小。堆中的具体内容有程序员安排
   
8. ## malloc/free，new/delete区别

   1. malloc/free是c++的标准库函数，new/delete是c++的运算符。他们都可以申请动态的内存和释放
   2. 对于**非内部数据类型**的对象而言，只用malloc和free无法满足动态对象的要求。对象在创建的时候要自动执行构造函数，对象在销毁的时候要执行析构函数。由于malloc/free是库函数而不是运算符，不在编译器控制权限之内，不能够把执行构造函数和析构函数的任务强加于malloc/free。因此C++语言需要一个能完成动态内存分配和初始化工作的运算符new，以一个能完成清理与释放内存工作的运算符delete。注意new/delete不是库函数
   3. c++程序经常需要调用c函数，而c程序只能用malloc和free管理内存。
   4. new可以认为是malloc加构造函数的执行，new出来的指针是直接带类型信息的，而malloc返回的都是viod指针。

9. 常见的内存错误以及对策

   1. 内存尚未分配成功，却使用了它；解决办法：在使用内存之前检查指针是否为nullptr。如果指针p是函数的参数，那么在函数的入口使用assert(p != NULL) 进行检查，如果是用malloc或者new来申请的，应该用`if (p == NULL)或者 if (p != NULL)`来进行防错处理。
   2. 内存分配虽然成功，但是尚未初始化就引用它。错误原因：意识没有初始化的概念，二是为一位内存的缺省值为0，导致引用初值错误。解决办法：要赋值。
   3. 内存分配成功并初始化，但是超过了内存的边界。常常出现在数组越界上。
   4. 忘记内存释放，造成内存泄漏；含有这种错误的函数每次被调用都会丢失一块内存，开始时内存充足，看不到错误，但终有一次程序会因为内存不足死掉。
   5. 释放了内存却继续使用它。产生原因：1.程序中的对象调用过于复杂，难以搞清楚某个对象就近是否被释放了内存，此时应该重新设计数据结构，从根本上解决对象管理混乱的局面。2.使用free/delete释放内存后没有将指针设置为null，导致产生野指针。

10. ## 内存管理需要遵守的原则：

    1. 使用molloc/new申请内存后需要检查指针值是否为null，防止使用指针值为null的指针
    2. 不要忘记数组和动态内存赋初值，防止未被初始化的内存做为右值。
    3. 避免数组或者指针下表越界，特别当心++和--操作
    4. 动态内存的申请和释放必须配对，防止内存泄漏
    5. 用free或者detele之后要立即将指针设置为null，防止产生野指针。

11. ## 字节对齐问题

    1. 为什么要使用字节对齐？

       字节对齐是c/c++的编译器的一种技术手段，主要在可接受浪费空间的前提下，提高内存的访问速度。比如32位系统，4字节对齐能使cpu访问速度提高。

    2. 字节对齐的原则：

       1. 结构体中每个成员相对于结构体首地址的偏移量是成员大小的整数倍，如有需要编译器会填充字节。
       2. 结构体的总大小是结构体最宽的基本类型成员大小的整数倍，如有需要编译器会填充字节。

       ```cpp
       // 用于测试的结构体
       typedef struct MemAlign
       {
       	char a[18];	// 18 bytes
       	double b;	// 08 bytes	
       	char c;		// 01 bytes
       	int d;		// 04 bytes
       	short e;	// 02 bytes
       }MemAlign;
       
       //大小为：48字节
       
       ```

       对于union：sizeof(union)，以结构里面size最大元素为union的size,因为在某一时刻，union只有一个成员真正存储于该地址。

12. ## 0的比较判断，浮点数存储

    ```cpp
    1. int型变量
    if ( n == 0 )
    if ( n != 0 )
    2. bool型
     if (value == 0)
    if (value != 0)
    3. char*型
    if(p == NULL) / if(p != NULL)
    5. 浮点型
    const float EPSINON = 0.0000001;
    if ((x >= - EPSINON) && (x <= EPSINON)
    
    ```

13. ## 内联函数有什么优点？内敛函数和宏定义的区别

    优点：函数会在它所调用的位置上展开。这么做可以消除函数调用和返回所带来的开销（寄存器存储和恢复），而且，由于编译器会把调用函数的代码和函数本身放在一起优化，所以也有进一步优化代码的可能。
    内联函数使用的场合：对于简短的函数并且调用次数比较多的情况，适合使用内联函数

    ##### 内联函数和宏定义区别：

    1)内联函数在编译时展开，而宏在预编译时展开
    2)在编译的时候，内联函数直接被嵌入到目标代码中去，而宏只是一个简单的文本替换。
    3)内联函数可以进行诸如类型安全检查、语句是否正确等编译功能，宏不具有这样的功能。
    4)宏不是函数，而inline是函数

14. ## 调用惯例及printf变参实现
    函数在被调用时，函数的调用方和被调用方对于函数时如何调用的必须有一个明确的规定。只有双方同时遵循同样的规定，函数才能够被正确调用。这样的规定被称为：调用惯例。

    函数的调用惯例包含两个方面：

    1.函数参数的传递顺序和方式

    函数的传递有很多种方式，最常见的是通过栈传递。函数的调用方将参数压入栈中，函数自己再从栈中将参数取出。对于有多个参数的函数，调用惯例要规定函数调用方将参数压栈的顺序，是从左往右压栈，还是从右往左压栈。

    2.栈的维护方式

    在函数将参数压入栈中之后，函数体会被调用，此后需要将被压入的参数全部弹出，使得栈在函数调用前后保持一致。这个弹出的工作可以由函数调用方来完成，也可以由函数本身来完成。在不指定调用惯例的情况下，默认采用cdecl惯例。![](https://img-blog.csdn.net/201809221853016?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM0NTcxNjc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

15. ## 覆盖、重载、隐藏的区别
    （1）重载：重载翻译自overload，是指同一可访问区内被声明的几个具有不同参数列表（参数的类型，个数，顺序不同）的同名函数，根据参数列表确定调用哪个函数，重载不关心函数返回类型。
    （2）重写：重写翻译自override，是指派生类中存在重新定义的函数。其函数名，参数列表，返回值类型，所有都必须同基类中被重写的函数一致，只有函数体不同。

    1.成员函数被重载的特征：

    （1）相同的范围（在同一个类中）；

    （2）函数名字相同；

    （3）参数不同；

    （4）virtual 关键字可有可无。

    2.覆盖是指派生类函数覆盖基类函数，特征是：

    （1）不同的范围（分别位于派生类与基类）；

    （2）函数名字相同；

    （3）参数相同；

    （4）基类函数必须有virtual 关键字。

    3.“隐藏”是指派生类的函数屏蔽了与其同名的基类函数，规则如下：

    （1）如果派生类的函数与基类的函数同名，但是参数不同。此时，不论有无virtual关键字，基类的函数将被隐藏（注意别与重载混淆）。

    （2）如果派生类的函数与基类的函数同名，并且参数也相同，但是基类函数没有virtual 关键字。此时，基类的函数被隐藏（注意别与覆盖混淆）。

16. ## 四种强制类型转换
    static_cast( expression )
    用于数值类型之间的转换，也可以用于指针之间的转换，编译时已经确定好，效率高，但需要保证其安全性。
    a)	指针要先转换成void才能继续往下转换。
    b)	在基类和派生类之间进行转换（必须有继承关系的两个类）
    子类对象可以转为基类对象(安全)，基类对象不能转为子类对象(可以转换，但不安全，dynamic_cast可以实现安全的向下转换)。
    static_cast不能转换掉expression的const、volatile、或者__unaligned属性
    dynamic_cast < type-id> ( expression )
    只能用于对象的指针和引用之间的转换，需要虚函数。
    dynamic_cast会检查转换是否会返回一个被请求的有效的完整对象，否则返回NULL；
    Type-id必须是类的指针、类的引用或者void *，用于将基类的指针或引用安全地转换成派生类的指针或引用。
    const_cast < type-id> ( expression )
    这个转换类型操纵传递对象的const属性，或者是设置或者是移除。

    reinterpret_cast < type-id> ( expression )
    用在任意指针类型之间的转换；以及指针与足够大的整数类型之间的转换，从整数到指针，无视大小。

    隐式类型转换
    两种常用的实现隐式类类型转换的方式：
    a、使用单参数的构造函数或N个参数中有N-1个是默认参数的构造函数。
    b、使用operator目标类型（） const

    避免隐式转换：前面加explicit。

17. 关键字static
    预备知识—程序的内存分配 
    一个由C/C++编译的程序占用的内存分为以下几个部分

    栈区（stack）— 由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。 
    堆区（heap） — 一般由程序员分配释放，若程序员不释放，程序结束时可能由OS回收 。注意它与数据结构中的堆是两回事，分配方式倒是类似于链表。 
    全局区（静态区）（static）—，全局变量和静态变量的存储是放在一块的，初始化的全局变量和静态变量在一块区域， 未初始化的全局变量、未初始化的静态变量在相邻的另一块区域。 - 程序结束后有系统释放 
    文字常量区—常量字符串就是放在这里的。程序结束后由系统释放 
    程序代码区—存放函数体的二进制代码。

    一个正常的程序在内存中通常分为程序段、数据端、堆栈三部分。程序段里放着程序的机器码、只读数据，这个段通常是只读，对它的写操作是非法的。数据段放的是程序中的静态数据。动态数据则通过堆栈来存放。

    在内存中，它们的位置如下： 
    +——————+ 内存低端 
    | 程序段 | 
    |——————| 
    | 数据段 | 
    |——————| 
    | 堆栈 | 
    +——————+ 内存高端

    修饰全局变量

    存储方式： 
    全局变量(外部变量)的说明之前再冠以static 就构成了静态的全局变量。

    全局变量本身就是静态存储方式， 静态全局变量当然也是静态存储方式。 这两者在存储方式上并无不同。

    作用范围： 
    普通的全局变量的作用域是整个源程序， 当一个源程序由多个源文件组成时，普通的（也就是非静态）的全局变量在各个源文件中都是有效的。

    而静态全局变量则限制了其作用域， 即只在定义该变量的源文件内有效， 在同一源程序的其它源文件中不能使用它。由于静态全局变量的作用域局限于一个源文件内，只能为该源文件内的函数公用， 因此可以避免在其它源文件中引起错误。　

    修饰局部变量 
    生存周期 —变量从定义到销毁的时间范围 
    普通局部变量存储在栈中,数据则随着函数等的作用域结束导致出栈而销毁。 
    静态局部变量存放在静态区,所以它的生命周期和全局变量相同是从程序开始到程序结束。

    初始化 
    静态局部变量只能被初始化一次.

    修饰函数 
    作用范围： 
    static global函数将其作用域限制在本源文件之内，对其它源文件不可见。而global函数具有全局可见性，对其它源文件则可见。

    内存： 
    static函数在内存中只有一份，普通函数在每个被调用中维持一份拷贝。

    此处static的含义和全局变量相同,不是指存储方式，而是指对函数的作用域仅局限于本文件(所以又称内部函数)。 
    定义静态函数的好处： 静态函数不能被其它文件所用； 其它文件中可以定义相同名字的函数，不会发生冲突；

    > ## 总结
    >
    > ###  把局部变量改变为静态变量后，是改变了它的存储方式，即改变了它的生存期。
    >
    > ### 把全局变量改变为静态变量后，是改变了它的作用域， 限制了它的使用范围。

    

    

