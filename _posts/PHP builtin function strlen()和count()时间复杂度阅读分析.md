title: PHP builtin function strlen()和count()时间复杂度阅读分析
categories: PHP##分类
tags: [PHP,内核,源码]##标签，多标签格式为 [tag1,tag2,...]
keywords: PHP,内核,源码##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 从源码角度分析strlen()和count()时间复杂度
date: 2017/01/23 14:00:25
---

翻了下PHP内核的定义，大概心中也有了答案了

count()和strlen()都是O(1)的时间复杂度

试想一下如果strlen()需要O(N)的复杂度那未免也太慢了，字符串长度起来的话服务器不是要直接挂掉吗

这两个函数都是典型的空间换时间的做法

我们可以先看看zvalue的结构：

typedef union _zvalue_value {
   long lval;             /* long value */
   double dval;            /* double value */
   struct {
      char *val;
      int len;
   } str;
   HashTable *ht;          /* hash table value */
   zend_object_value obj;
   zend_ast *ast;
} zvalue_value;
这里用的是一个联合体，当变量类型是string类型的时候附加存储多了一个len的整型变量，显而易见需要取长度直接利用记录值就可以了，自然就是O(1)

对于count()常用的参数类型应该为数组，对于继承Countable的类暂不作讨论

数组实现方式为Hashtable，直接看看他的结构吧

typedef struct _hashtable { 
    uint nTableSize;        // hash Bucket的大小，最小为8，以2x增长。
    uint nTableMask;        // nTableSize-1 ， 索引取值的优化
    uint nNumOfElements;    // hash Bucket中当前存在的元素个数，count()函数会直接返回此值 
    ulong nNextFreeElement; // 下一个数字索引的位置
    Bucket *pInternalPointer;   // 当前遍历的指针（foreach比for快的原因之一）
    Bucket *pListHead;          // 存储数组头元素指针
    Bucket *pListTail;          // 存储数组尾元素指针
    Bucket **arBuckets;         // 存储hash数组
    dtor_func_t pDestructor;    // 在删除元素时执行的回调函数，用于资源的释放
    zend_bool persistent;       //指出了Bucket内存分配的方式。如果persisient为TRUE，则使用操作系统本身的内存分配函数为Bucket分配内存，否则使用PHP的内存分配函数。
    unsigned char nApplyCount; // 标记当前hash Bucket被递归访问的次数（防止多次递归）
    zend_bool bApplyProtection;// 标记当前hash桶允许不允许多次访问，不允许时，最多只能递归3次
#if ZEND_DEBUG
    int inconsistent;
#endif
} HashTable;
count直接获取nNumOfElements大小，所以也是O(1)