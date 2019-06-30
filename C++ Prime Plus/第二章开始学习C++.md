## C++输入输出
```C++
#include <iostream> 
std::cout<<""<<std::endl;
int a;
std::cin>>a;
```
## C语言的输入输出
```C++
#include <stdio.h>
float a;
scanf("%d",&a); 
long double a = 3.14;
printf("a = %08.3lf",a);     //右对齐，开头补零，字符宽度8位，精度3位，以long double型输出。
```
C++中重载了运算符<< 来实现不同类型的输出/输入，用户自己的类也可以重载该运算符类实现输出<br>
还可以通过设置cin的其他属性来实现类似于printf的格式化输出

