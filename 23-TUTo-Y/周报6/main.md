##  异或运算符交换两个变量的值
使用如下代码可以交换两个变量的值
```C
#include <stdio.h>
void swap(int *v1, int *v2)
{
    (*v1) ^= (*v2) ^= (*v1) ^= (*v2);
}
int main()
{
    int a = 123;
    int b = 456;
    swap(&a, &b);
    printf("a = %d\nb = %d\n", a, b);
    return 0;
}
```
运行结果是将a的值和b的值进行了交换，但是当传入的参数都是a的地址时:
```C
#include <stdio.h>
void swap(int *v1, int *v2)
{
    (*v1) ^= (*v2) ^= (*v1) ^= (*v2);
}
int main()
{
    int a = 123;
    swap(&a, &a);
    printf("a = %d\n", a);
    return 0;
}
```
结果a的值变成了0，并且这种交换并不能提高程序的运行速率。

##  逻辑右移
*   逻辑右移:向右移n位，并在左边补n个0
*   算术右移:向右移n位，并在左边补n个最高位的值

在程序进行位移运算时，C语言标准并没有明确定义应该使用哪种类型的右移。对于无符号数据右移是逻辑右移，对于有符号数据，几乎所有的编译器都使用算术右移
但是java有着明确的定义:x>>n使用算术右移，x>>>n使用逻辑右移

```C
#include <stdio.h>
int main()
{
    int          a = 0x87654321;
    unsigned int b = 0x87654321;
    a >>= 3;
    b >>= 3;
    printf("a = %x\nb = %x\n", a, b);
    return 0;
}
```
计算结果:`a`=0xf0eca864, `b`=0x10eca864

实际上的位移量是k%w得到的(w是变量占有的位数，k是移动的位数)
比如`int a = 0x1234`, 那么`a >> 3`和`a >> 35`的结果是一样的