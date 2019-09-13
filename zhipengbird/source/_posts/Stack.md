---
title: 栈
date: 2019-09-08 10:51:03
tags:
- 数据结构
---

> 栈和队列是在程序设计中被广泛使用的的两种线性数据结构，它们的特点在于基本操作的特殊性，栈必须按“后进先出”的规则进行操作，而队列必须按“先进先出”的规则进行操作。和线性表相比，它们的插入和删除操作受更多的约束和限制，故又称限定性的线性表结构

# 栈
“洗干净的盘子总是逐个往上叠在已洗好的盘子上面，而用的时候从上往下逐个取用，即后洗好的盘子比先洗好的盘子先被使用”，栈的操作特点正是上述的实际抽象。

栈(stack)是限定只能在表的一端进行插入和删除操作的线性表。在表中允许插入删除的一端称为“栈顶（top）”，不允许插入删除的另一端称为“栈底（bottom）”。 如下图所示的栈中，a1是栈底元素，an是栈顶元素。栈中元素以a1,a2,a3,...,an的顺序进栈，则出栈的第一个元素是an,即栈的修改是按后进先出的原则进行的。因此栈又称LIFIO（last in first out）表。

## 栈的表示和操作实现
和线性表类似，栈也有两种存储表示方式：顺序栈和链栈
### 顺序栈
顺序栈指的是利用顺序存储分配实现的栈。即利用一组地址连续的存储单元依次存放自栈底到栈顶的数据元素，同时附设指针top指示栈顶元素在顺序栈中的位置。类似于顺序表，用一维数组描述顺序栈中数据元素的存储区域，并预设一个数组的最大空间。通常做法以*top=0*表示“空栈”
```C++
typedef struct YStack {
    int *elem;
    int top;
    int size;
}YStack;

void initStack(YStack &stack, int maxSize = 20){
    //构造一个空栈stack,初始分配的最大空间为maxSize
    //1.为顺序栈分配一个最大容量为maxSize的数组空间
    stack.elem = new int[maxSize];
    stack.top = -1;//当前顺序栈中所含的元素个数为0
    stack.size = maxSize;///该顺序栈最多可容纳maxSize个元素
}
bool popElement(YStack &stack, int &elem){
    //若栈不为空，则用elem返回stack的栈顶元素，并返回true,否则返回false
    if (stack.top == -1) {
        return false;
    }
    elem = stack.elem[stack.top];
    return true;
}
void push(YStack &stack, int elem){
    //插入元素elem为新的栈顶元素
    if (stack.top < stack.size){
        stack.elem[++stack.top] = elem;
    }else{
        //TODO::当前元素个数已达最大值，需要进行扩容
        printf("当前元素个数已达最大值，需要进行扩容");
    }
}
bool pop(YStack &stack, int &elem){
    //若栈不之为空，则返回stack的栈顶元素，用elem返回其值，并返回true
    if (stack.top == -1) {
        return false;
    }
    elem = stack.elem[stack.top--];
    return true;
}
```
### 链栈
链栈指的是利用链式分配实现的栈。链栈的结点结构和链表的结点的结构相同，值得注意的是，**链栈中指针的方向是从栈顶指向栈底。**
```c++
typedef struct YStackNode{
    int data;
    struct YStackNode *next;
}YStackNode,*YStackLink;

void initStackLink( YStackLink  &stack){
    //构造一个空的栈链，即设栈顶指针为空
    stack = NULL;
}
void push(YStackLink &stack, int elem){
//    在链栈的顶插入新的栈元素elem
    YStackLink node  = new YStackNode;//为新的栈顶元素分配结点
    node->data = elem;
    node ->next = stack;///插入新的栈顶元素
    stack = node;///修得栈顶指针
  
}
bool pop(YStackLink  &stack ,int &elem){
    //若栈不为空，删除栈顶元素，以elem返回其值，并返回true，否则返回false
    if (stack) {
        YStackLink p = stack;
        stack = stack -> next;
        elem = p -> data;
        delete p;
        return true;
    }
    return false;
}
```
# 队列 
数据结构“队列”与生活中的“排队”极为相似，也是按“先到先办”的原则办事，并且严格限定：*即不允许“加塞儿”，也不允许“中途离队”*
队列（queue）是限定只能在表的一端进行插入，在表的另一端进行删除的线性表。表中允许插入的一端称为“队尾(rear)”，允许删除的一端称为“队头(font)”,如下图所示队列中，a1队头元素，an队尾元素，队列中的元素以a1,a2,a3,...,an的次序依次入队，则也只能依相同的次序退出队列。即a1是第一个出队的元素，只有在a2,a3,....,an-1都离开队后，an才能出队列。队列的修改是依“先进先出”的原则进行的，因此队列又称FIFO（first in first out ）表。

## 队列的表示和操作实现
和线性表类似，队列的存储方式有：链队列和循环队列
### 链队列
用链表表示的队列简称为*链队列*。由队列的结构特性容易想到，一个链队列显示然需要两个分别指向队头和队尾的指针（分别称为头指针和尾指针）。为操作方便，为链队列添加一个“头结点”，并约定头指针始终指向这个附加的头结点，尾指针指向真正的队尾元素结点。一个“空”的链队列只包含一个头结点，并且队列的头指针和尾指针都指向这个头结点。
### 循环队列

