# 主页

For full documentation visit [mkdocs.org](https://www.mkdocs.org).

## Code Annotation Examples

### Codeblocks

```c title="hello" linenums="1"
#include <stdio.h>
#include <malloc.h>

typedef struct List List;
typedef struct StackTop StackTop;
typedef struct ListNode ListNode;
// 所有节点序号从0开始，和数组一样
struct ListNode
{
    int value;             // 储存链表每个节点的值
    struct ListNode *next; // 一个和链表节点一样结构的结构指针，用来指向下一个节点
};
struct List
{
    int value;
    struct List *next;
};
struct StackTop // 栈顶，用于维护整个栈
{
    int size;  // 栈的大小
    List *top; // 栈顶指针，用于指栈的顶元素
};
ListNode *NewList(int);                    // 为什么要加*呢，因为我要返回一个指针，如果是返回一个结构就完蛋了，因为无法和下一个节点连起来
ListNode *Access(ListNode *, int);         // 输入头节点和节点序号，返回目标节点
void Delete(ListNode *);                   // 销毁链表
void Insert(ListNode *, ListNode *, int);  // 插入第几号之后
int Find(ListNode *, int);                 // 查找节点值，查到了返回节点序号，没查到返回-1
void Create(ListNode *[], int[], int len); // 创建一个链表

StackTop *NewStack();         // 新建一个栈
void DestoyStack(StackTop *); // 销毁一个栈
int Peek(StackTop *);         // 返回栈顶元素
void Push(StackTop *, int);   // 入栈
int Pop(StackTop *);          // 出栈，并返回栈顶元素的值
int IsEmpty(StackTop *);      // 返回是否空栈，空则返回1，非空则返回0
int Size(StackTop *);         // 返回栈的元素个数

int main()
{
    StackTop *MyStack = NewStack();
    ListNode *MyList = NewList(-1); // 链表头节点，啥都不存
    int tmp, len;
    printf("请输入要入栈的元素（循环输入）：\n");
    while (scanf("%d", &tmp) && tmp != EOF && tmp != '\n')
    {
        Push(MyStack, tmp);
    }
    len = Size(MyStack);
    for (int i = 1; i <= len; i++)
    {
        ListNode *tmp = NewList(Pop(MyStack));
        Insert(MyList, tmp, i);
    }
    printf("现在的链表是：\n");
    for (int i = 1; i <= len; i++)
    {
        printf("%d ", Access(MyList, i)->value);
    }
    DestoyStack(MyStack);
    Delete(MyList);
    return 0;
}
StackTop *NewStack()
{
    StackTop *stack;
    stack = (StackTop *)malloc(sizeof(StackTop));
    stack->top = NULL;
    stack->size = 0;
    return stack;
}
void DestoyStack(StackTop *stack)
{
    while (stack->top != NULL)
    {
        List *list = stack->top->next; // 缓存以下头节点指向节点的下一个
        free(stack->top);
        stack->top = list;
    }
    free(stack); // 把栈顶也捎带手销毁
}
int Peek(StackTop *stack)
{
    if (stack->size == 0)
    { // 特判空栈的情况
        return __INT_MAX__;
    }
    return stack->top->value;
}
void Push(StackTop *stack, int val)
{                                              // 头插法
    List *node = (List *)malloc(sizeof(List)); // 创建一个新节点用于储存新节点
    node->value = val;
    node->next = stack->top;
    stack->top = node;
    stack->size++;
}
int Pop(StackTop *stack)
{
    if (stack->size == 0)
    { // 空栈则返回INT_MAX表示错误
        return __INT_MAX__;
    }
    List *temp = stack->top;
    stack->top = stack->top->next;
    stack->size--;
    return temp->value;
}
int IsEmpty(StackTop *stack)
{
    if (stack->size == 0)
    {
        return 1;
    }
    else
    {
        return 0;
    }
}
int Size(StackTop *stack)
{
    return stack->size;
}

void Create(ListNode *n[], int val[], int len)
{
    for (int i = 0; i < len; i++)
    {
        n[i] = NewList(val[i]);
    }
    for (int i = 0; i < len - 1; i++)
    {
        n[i]->next = n[i + 1];
    }
}
ListNode *NewList(int value)
{
    ListNode *node;
    node = (ListNode *)malloc(sizeof(ListNode)); // 给node一点内存，大小就是一个结构的大小，类型是指针
    node->value = value;
    node->next = NULL; // 一定要有，不然无法判定是否到末尾了
    return node;
}
ListNode *Access(ListNode *head, int index)
{
    while (head != NULL && head->next != NULL && index > 0)
    {                      // 头节点不是空的、且下一个节点不是空的、且还没访问到目标节点的序号
        head = head->next; // 头节点就变成下一个节点了，然后继续循环
        index--;
    }
    return head; // 把头节点返回去就行，因为头节点已经指向了我们要找的节点
}
void Insert(ListNode *head, ListNode *target, int index)
{ // 插入第几号之后
    while (head != NULL && head->next != NULL && index > 0)
    {
        head = head->next;
        index--;
    }
    target->next = head->next; // 先让插入节点指下一个，然后再让前边的节点指插入的，如果不这样子链表会丢掉后面的所有节点
    head->next = target;
}
void Delete(ListNode *head)
{
    ListNode *tmp;
    while (head->next != NULL) // 遍历到下一个，并且free掉刚才的那个节点
    {
        tmp = head;
        head = head->next;
        free(tmp);
    }
    free(head);
}
int Find(ListNode *head, int key)
{
    int index = 0;
    while (head != NULL)
    {
        if (head->value == key)
        {
            return index;
        }
        head = head->next;
        index++;
    }
    return -1;
}
void Remove(ListNode *head, int index)
{
    while (head && head->next && index > 1)
    {
        head = head->next;
        index--;
    }
    ListNode *temp = head->next; // 直接将删除节点之前的一个节点指到删除节点之后，就完事了
    head->next = temp->next;
    free(temp); // 现在temp指的就是要删除的节点，free了就可以
}
```