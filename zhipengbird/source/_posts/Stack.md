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
```c++
typedef int ValueType;
typedef struct QueueNodel {
    ValueType data;
    struct QueueNodel *next;///<下一个指针
}QueueNodel , *LinkQueueList,*QueurPtr;

///<链式队列
typedef struct LinkQueue {
    QueurPtr front;///<队头指针
    QueurPtr rear;///<队尾指针
}LinkQueue;
void initLinkQueue(LinkQueue &queue){
    queue.front = queue.rear = new QueueNodel;
    queue.front->next = nullptr;
}

void destroyLinkQueue(LinkQueue & queue){
    while (queue.front) {
        queue.rear =queue.front->next;
        delete queue.front;
        queue.front = queue.rear;
    }
}

void enLinkQueue(LinkQueue &queue, ValueType value){
    LinkQueueList node = new QueueNodel;
    node->data = value;
    node->next = nullptr;
    queue.rear->next = node;
    queue.rear = node;
}

bool deLinkQueue(LinkQueue &queue, ValueType &value){
    if (queue.front == queue.rear) {
        return false;
    }
    LinkQueueList node  = queue.front->next;
    value = node->data;
    queue.front->next = node->next;
    if (queue.rear== node) {
        queue.rear = queue.front;
    }
    delete node;
    return true;
}
```
### 循环队列
和顺序栈相类似，在利用顺序分配存储结构实现队列时，除了用一维数组描述队列中数据元素的存储区域，并预设一个数组的最大空间之外，尚需要设置两个指针front和rear分别指向队头和队尾。
为叙述方便，约定：初始化建空队列时，令front= rear = 0;每当插入一个新元素后，队尾指针rear增加1，每当删除一个队头元素时，front增加1。因此在非空队列中，头指针指向队头元素，尾指针指向队尾元素的下一个位置。
为防止数组越界，一个较巧妙的方法是将顺序队列想像成一个首尾相接的环状空间。对于循环队列，不能以头、尾指针是否相同来判断队列的“满”或“空”。
可以有两种处理方式：
1. 附设一个标志位以区别队列空间“满”“空”
2. 少用一个元素空间，约定"队尾指针在队头指针的前一个位置（指环状队列中的前一位置）"为队满。
循环队列中头、尾指针“依环状增1”的操作可用“模”来实现。通过取模，头指针和尾指针就可以在顺序空间内按头尾衔接的方式“循环”移动。
```c++
const int  QUEUE_INIT_SIZE = 100;///循环队列默认初始分配最大空间
const int  QUEUEINCREMENT = 10;///增补空间

typedef int QueueValueType;
typedef  struct YCircleQueue {
    QueueValueType *values;///存言储队列元素的数组
    int front;//头指针，若队列不为空，指向队头元素
    int rear;//尾指针，若队列不为空，指向队尾元素的下一个位置
    int queuesize;//队列当前的最大容量
    int incrementsize;//约定扩容增加
}YCircleQueue;

void initCircleQueue(YCircleQueue &queue, int maxsize ,int incrementsize){
    ///构建一个空队列，
    ///为队列分配（比实际能用多一个元素的）空间
    queue.values = new QueueValueType[maxsize + 1];
    queue.queuesize = maxsize;
    queue.incrementsize = incrementsize;
    queue.front = queue.rear = 0;
}

int circlequeuelength(YCircleQueue &queue){
    ///返回队列中的元素即队列当前长度
    return (queue.rear - queue.front + queue.queuesize ) %queue.queuesize;
}

bool deCircleQueue(YCircleQueue &queue, QueueValueType &value){
    ///若队列不为空，则删除队头元素，用value返回其值，并返回true
    if (queue.front == queue.rear) {
        return false;
    }
    value = queue.values[queue.front];
    queue.front = (queue.front +1)%queue.queuesize;
    return true;
}
void enCircleQueue(YCircleQueue &queue, QueueValueType value){
    //队满了，扩容
    if ((queue.rear + 1 )%queue.queuesize == queue.front ) {
        incrementCircleQueueSize(queue);
    }
    //插入新元素value为新的队尾元素
    queue.values[queue.rear] = value;
    queue.rear = (queue.rear +1) %queue.queuesize;
}
void incrementCircleQueueSize(YCircleQueue &queue){
    //为队列扩容
    QueueValueType *a  = new QueueValueType[queue.queuesize + queue.incrementsize];
    for (int k  = 0; k < queue.queuesize-1; k++) {
        //挪原队列元素
        a[k] = queue.values[(queue.front +k)%queue.queuesize];
    }
    delete queue.values; ///释放原有数组空间
    queue.values = a;///为队列设置新的数组
    queue.front = 0;//重置头指针
    queue.rear = queue.queuesize - 1;//重置尾指针
    queue.queuesize += queue.incrementsize;
}
```

扩容操作比一次性申请空间要费时间，一般大多数的问题常常可以根据问题的性质/规模估计队列的大小，而在无法预先估计队列可能达到的容量时，最好还是采用链队列。