const引用问题：
普通变量不可引用const
const引用可以引用const变量和普通变量
``` c

```



const指针问题
``` c
int v1 =1,v2 = 2;
const int *c = &v1;
int *const c2 = &v2;
const int *const c3 = &v2;
c = &v1; //正确，const *可以改变引用的变量
*c2 = 100; //正确，*const可以改变引用变量的值


*c = 100; //错误
c2 = &v1; //错误
c3 = &v2; //错误，两个const的情况不能更改引用和引用变量的值
*c3 = 10000; //错误
```