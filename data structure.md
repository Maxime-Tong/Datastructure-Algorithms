# 数据结构
## 线性表、链表
+  顺序存储：物理相邻，逻辑相邻

    特点：插入删除慢，查询快

+  链接存储：物理不相邻，逻辑相邻
    
    特点：插入删除快，查询慢

    `单链表的私有成员`
    ```cpp
    private:
        struct node{...};
        node* head;
        int len;

        //this function is effective
        node *move(int i)const 
        {
            node* p=head;
            while(i-->=0)p=p->nxt;
            return p;
        }
        //返回第i个节点的地址
    ```

    `双链表`

    私有成员比单链表多一个尾结点

    `单循环链表`

    不需要头结点，只需要指向线性表起始节点的指针head，如果head为空，表示空表。
+  链表的数组实现

    ```cpp
        struct node{
            int val;//也可以用数组下标表示存储的值
            int l,int r;
        }a[max];
    ```
## 栈
+ 实现

    线性栈、链接栈
+ 应用

    递归消除、括号配对、算术表达式的运算

    中缀表达式与后缀表达式的相互转化：

    1. 中缀表达式 -->后缀表达式：
        >将运算符放置在运算数之后
        
        如：5*(7-2*3)-8/2  --> 5.7.2.3.\*-\*8.2./-
        
        //下划线或者.来分割不同的数字

    2. 后缀表达式 -->中缀表达式：
        >根据原理逆向推导

    3. 中缀表达式转化为后缀表达式的程序实现

        <font color=yellow>关键在于依据优先级和结合性重新排列运算符</font> 

        运算符优先级：

        右括号 > 乘方（右结合） > 乘除 > 加减（左结合）> 左括号
            
        ```
        enum token
        {OPAREN,ADD,SUB,MULTI,DIV,EXP,CPAREN,VALUE,EOL}
        
        按照上述方式定义可以解决优先级的问题，token可以直接比较大小 
        ```
        
        <font color=red>核心思想：
        依据栈的特性，当遇到优先级低的运算符，表明前面的运算符可以计算，输出；遇到优先级高的运算符，不能确定能否进行计算，先压入栈中保存。
        </font>

        ```
        步骤：
        从左至右读取中缀表达式
        {
            if:
            {
                数字：直接输出
            运算符：
                if:
                比栈顶优先级高：意味着尚且不能计算
                比栈顶优先级底：意味着前面的运算符可以计算，弹出
                栈顶优先级同级：按照结合性决定是否弹出
                    if 
                    左结合：说明前面的都能运算
                    右结合：不能运算
            左括号：
                直接入栈
            右括号：
                一直弹出直到左括号
            }
            入栈读取的运算符
        }
        按顺序依次出栈剩余运算符
        ```
## 队列
+ 实现

    循环队列
    共MaxSize 个单元的连续空间
    ```cpp
    //入队操作
    rear=(rear+1)%MaxSize; 
    elem[rear]=x;
    
    //出队操作
    front=(front+1)%MaxSize;
    ```
    <font color=whiteblue>循环队列为了区分队列满和队列空的情况，通常采取牺牲一个单元的方法。(font/rear指向空单元)</font>
+ 应用
    
    火车车厢重排系统和单服务台排队系统模拟器

## 并查集

+ 应用

    处理不相交的集合的合并和查询问题,凡是涉及分组的问题都可以尝试用并查集解决。

+ 实现

### 初始化
```cpp
    int fa[MAXN]
    void init(int n)
    {
        for(int i=1;i<=n;++i)
            fa[i]=i;
    }
```
### 查询（包含路径压缩）
<font color=whitegreen>判断任意两个元素是否属于同一个集合，只需要判断是否拥有同一个祖先即可。路径压缩以提高并查集效率</font>
```cpp
int find(int x)
{
    int tmp=x;
    while(fa[x]!=x)
    {
        x=fa[x];
    }

    //路径压缩
    fa[tmp]=x;

    return x;
}
```

递归版本
```cpp
int find(int x)
{
    return x == fa[x] ? x : (fa[x] = find(fa[x]));
}
```
### 合并
```cpp
void merge(int i,int j)
{
    fa[find(j)]=find(i);
}
```
### 扩展域
P2024 食物链 P1525 关押罪犯

用于增加并查集的更多分类关系

```
merge(x,y+n)
可以用于表示x 和 y的天敌是同一类。
```

## 树状结构
### 遍历
#### 种类
前序遍历、中序遍历、后序遍历
（根节点的遍历位置决定）

层序遍历
#### 构造
由前序/后序遍历+中序遍历可以得到完整二叉树

+ 方法
  
  以前序遍历+中序遍历为例：
  按照前序遍历获得根节点，由中序遍历查找根节点的位置，其左右两边则代表着左子树和右子树

### 顺序实现
lch: k<<1

rch: k<<1|1

parent:k>>1
#### 前中后序遍历的非递归实现
1. 前序遍历
   ```cpp
   void preOrder()
   {
       linkStack<Node*>s;
       Node* current;
       s.push(root)
       while(!s.empty())
       {
           current=s.pop();
           cout<< current->data;
           if(current->right)s.push(current->right);
           if(current->left)s.push(current->left);
       }
   }
   ```
2. 中序遍历
   ```cpp
   struct StNode
   {
       Node* node;
       int TimesPop;
       StNode(Node*N):node(N),TimesPop(0){}
   }

   void midOreder()
   {
       linkList<StNode*>s;
       StNode current(root);
       s.push(current);
       while(!s.empty())
       {
           current=s.pop();
           if(++current->TimesPop==2)//根节点第二次出栈
           {
               //关键点 优先级高的节点后入栈
               cout<<current->data;
               if(current->right)
                    s.push(StNode(current->right));
           }
           else
           {
               s.push(current);
               if(current->left)
                    s.push(StNode(current->left));
           }
       }
   }
   ```
3. 后序遍历
   ```cpp
   void postOrder()
   {
       linkList<StNode*>s;
       StNode* current(root);
       s.push(current);

       while(!s.empty())
       {
           current=s.pop();
           if(++current->TimesPop==3)//第三次出栈
                cout<< current->data;
            s.push(current);
            if(current->Times==2)//第二次入栈
            {
                if(current->right)
                    s.push(StNode(current->right));
            }
            else
            {
                if(current->left)
                    s.push(StNode(current-.left));
            }
       }
   }
   ```
#### 应用
中缀表达式 -> 表达式树 -> 后序遍历计算结果
### 哈夫曼树

+ 定义
  构建一棵树，所有字符都包含在叶结点上，权值大的叶子应当尽量靠近树根，使它的编码尽可能地短。```从根到存储该字符的叶节点的路径即为哈夫曼编码，可定义左支为0，右支为1。```
> 只要每个字符的编码和其他字符的编码前缀不同，此编码成为前缀编码。
+ 实现细节

    对于n个元素，需要2n大小的数组，[n,2n-1]存储叶子节点，1为根节点，归并生成的节点从n-1逐步递推到根节点。

### 树和森林

### 优先队列（堆）

+ 实现原理

    1. enQueue(const T& x):插入元素x；放在末尾，逐渐和父节点递归交换至合适位置。
    2. deQueue():删除顶部元素；将顶部元素变成末尾元素，找子女中最小的（小根堆）节点，交换位置至合适位置。
    3. buildHeap():将一棵完全二叉树变成堆，复杂度O（n）;从叶结点逆向层次调用percolateDown(i),使得堆自底向上逐步满足条件。`叶结点天生满足堆的有序性，因此只需要从编号最大的非叶子结点开始调用percolateDown(i)函数即可。` 【i=currentSize/2】
+ 代码实现（小根堆）
```cpp
template<class T>
class priorityQueue
{
private:
    T* elem;
    int currentSize,maxSize;

    void percolateDown(int i)
    {
        T tmp=elem[i];
        int child,hole=i;
        for(;hole*2<=currentSize;hole=child)
        {
            child=hole*2;
            if(child!=currentSize&&elem[child+1]<elem[child])
                child++;
            if(elem[child]<tmp)elem[hole]=elem[child];
            else break;
        }

        elem[hole]=tmp;
    }

    void buildHeap()
    {
        for(int i=currentSize/2;i>0;--i)
            percolateDown(i);
    }

public:
    priorityQueue():currentSize(0),maxSize(1000000){elem=new T[maxSize];}
    priorityQueue(const T* items,int size):maxSize(size+10),currentSize(size)
    {
        elem=new T[maxSize];
        for(int i=1;i<=currentSize;++i)
            elem[i]=items[i-1];
        buildHeap();
    }
    
    ~priorityQueue(){delete[]elem;}

    void enQueue(const T&x)
    {
        int hole=++currentSize;
        for(;hole>1&&x<elem[hole>>1];hole>>=1)
            elem[hole]=elem[hole>>1];
        elem[hole]=x;
    }

    T& top()const{return elem[1];}

    T deQueue()
    {
        T minItem=elem[1];
        elem[1]=elem[currentSize--];
        percolateDown(1);
        return minItem;
    }
}
```
+ 升级版堆
  1. D堆：每个结点存在D个儿子。主要被应用于插入操作比删除操作多很多的应用中，生成的堆较二叉堆更矮，插入操作会更快。
  2. 归并堆：左堆、斜堆、二项堆。归并两个堆的速率更快。

## 集合
### 排序
快排
```cpp
void qsort(int l,int r)//应用二分思想{
    int mid=a[(l+r)/2];//中间数
    int i=l,j=r;
    do{
        while(a[i]<mid) i++;//查找左半部分比中间数大的数
        while(a[j]>mid) j--;//查找右半部分比中间数小的数
        if(i<=j)//如果有一组不满足排序条件（左小右大）的数
        {
            swap(a[i],a[j]);//交换
            i++;
            j--;
        }
    }while(i<=j);//这里注意要有=
    if(l<j) qsort(l,j);//递归搜索左半部分
    if(i<r) qsort(i,r);//递归搜索右半部分
}
```
归并排序
![](image/mergesort.png)
### 查找

【内部查找】，一般以比较次数作为衡量时间性能的标准；【外部查找】，一般以外存储器的访问次数作为衡量标准。

1. 二分查找
2. 插值查找
3. 分块查找

### 二叉查找树

    【分类】
    查找树：处理动态查找表的树
    散列表：专门用于集合查找的数据结构

    【二叉查找树】
    def 
    1. 若左子树不为空，则左子树[所有]元素比根节点键值小
    2. 若右子树不为空，则右子树[所有]元素比根节点键值大
    3. 它的左右子树同样是二叉查找树

    重要性质
    中序遍历一颗二叉查找树即得到按键值递增次序的排列

    插入  递归过程
    1. 若根节点不存在，则插入为根节点
    2. 若根节点关键字大于插入元素关键字，则在左子树上插入
    3. 反之，在右子树上插入 

    删除
    1. 若删除关键字小于根节点，在左子树上删除结点
    2. 若删除关键字大于根节点，在右子树上删除结点
    3. 删除根节点。（1）若根结点是叶子，直接删除；（2）若根节点只有至多一个子节点，则将子结点直接连接在它的父亲节点；（3）若左右儿子都存在，则选取左子树的最大值或右子树的最小值作为根节点的替代，并删除该结点。
   
    时间代价
    最坏情况会退化为单链表，复杂度O（N），平均复杂度为O（logN）
### 示例代码
```cpp
template <class T>
class BinarySearchTree{
private:
    struct Node{
        T value;
        Node* left,*right;
        Node(const T& val,Node* lt=NULL,Node* rt=NULL):value(val),left(lt),right(rt){}
    };
    Node* root;
public:
    BinarySearchTree(){
        root = NULL;
    }

    ~BinarySearchTree(){makeEmpty(root);}

    void insert(T& x){
        insert(x,root);
    }

    void remove(T& x){
        remove(x,root);
    }

    T find(T& x){
        return find(x,root);
    }

private:
    void makeEmpty(Node* t){
        if(t==NULL)return;
        makeEmpty(t->left);
        makeEmpty(t->right);
        delete t;
    }

    void insert(T& x,Node*& t){
        if(t==NULL)t = new Node(x);
        else if(x<t->value){
            insert(x,t->left);
        }
        else if(x>t->value){
            insert(x,t->right);
        }
    }

    void remove(T& x,Node*& t){
        if(t==NULL)return;
        else if(x<t->value)remove(x,t->left);
        else if(x>t->value)remove(x,t->right);
        else if(t->left && t->right){
            Node* tmp=t->right;
            while(tmp->left)tmp=tmp->left;
            t->value = tmp->value;
            remove(t->value,t->right);
        }
        else{
            Node* oldNode=t;
            t = (t->left!=NULL)?t->left:t->right;
            delete oldNode;
        }
    }

    T find(T& x,Node* t){
        // self definition
        if(t==NULL)return INT_MAX;
        T cur;
        if(x<t->value){
            cur = t->value-x;
            return min(cur,find(x,t->left));
        }
        else if(x>t->value){
            cur = x-t->value;
            return min(cur,find(x,t->right));
        }
        else return 0;
    }
};

```
### AVL树
#### 定义
    一棵每个节点的左右子树高度差最多为1的二叉查找树。

#### 特性
    树中最少的结点树是其高度的指数函数，即高为H的树至少有C^H个结点

    最坏情况下的时间复杂度是对数级的

#### 插入操作
>AVL树的平衡策略保证了插入后至多只要调整一个节点的平衡！

    工具函数 LL LR RL RR 函数
    
    LL RR镜像对称，LR RL镜像对称；

    LL函数：（单旋转）
    当出现A的左子树比右子树高，插入发生在A的左儿子B的左子树中。（外侧）

    操作：让B作为树根，让A作为B的右子树，B的右子树作为A的左子树
    注意： t结点是引用传递
    void LL(AvlNode*& t)
    {
        AvlNode* t1=t->left;//未来的树根
        t->left=t1->right;
        t1->right=t;

        t->height=max(height(t->left),height(t->right))+1;
        t1->height=max(height(t1->left),height(t1->right))+1;

        t=t1;
    }

    LR函数：（双旋转）
    当出现A的左子树比右子树高，插入发生在A的左儿子B的右子树中。（内侧）

    操作：先对B做一次左旋转，再对A做一次右旋转

    void LR(AvlNode*& t)
    {
        RR(t->left);
        LL(t);
    }

#### Avl树模板代码
```cpp
template<class KEY,class OTHER>
struct SET
{
    KEY key;
    OTHER other;
};


template<class KEY,class OTHER>
class AvlTree {
 struct AvlNode
 {
     SET<KEY,OTHER> data;
     AvlNode *left;
     AvlNode* right;
     int height;

     AvlNode(const SET<KEY,OTHER>&elem,AvlNode* lt,AvlNode* rt,int h=1)
     :data(elem),left(lt),right(rt),height(h){}
 };

 AvlNode* root;

public:
    AvlTree(){root=NULL;}
    ~AvlTree(){clear(root);}
    SET<KEY,OTHER>*find(const KEY& x)const
    {
        AvlNode*t=root;

        while(t!=NULL&&t->data.key!=x)
        {
            t=(t->data.key<x)?t->right:t->left;
        }
        if(t==NULL)return NULL;
        else return t->data;
    };

    void insert(const SET<KEY,OTHER>&x)
    {
        insert(x,root);
    }

    void remove(const KEY& x)
    {
        remove(x,root);
    }

private:
    void insert(const SET<KEY,OTHER>&x,AvlNode*& t)
    {
        if(t==NULL)
            t=new AvlNode(x,NULL,NULL);
        else if(t->data.key>x.key)
        {
            insert(x,root->left);
            if(height(t->left)-height(t->right)==2)
            {
                if(x.key<t->left->data.key)LL(t);
                else LR(t);
            }
        }
        else if(t->data.key<x.key)
        {
            insert(x,root->right);
            if(height(t->left)-height(t->right)==2)
            {
                if(height(x.key<t->right->data.key))RL(t);
                else RR(t);
            }
        }
        
        t->height=max(height(t->left),height(t->right))+1;
    }
    
    bool remove(const KEY&x,AvlNode*&t)
    {
        if(t==NULL)return true;
        if(x==t->data.key)
        {
            if(t->left==NULL||t->right==NULL)
            {
                AvlNode* oldNode=t;
                t=(t->left)?t->left:t->right;
                delete oldNode;
                return false;
            }
            else
            {
                AvlNode* tmp=t->right;
                while(tmp->left)tmp=tmp->left;
                t->data=tmp->data;
                if(remove(tmp->data.key,t->right))return true;//删除后右子树没有变矮
                return adjust(t,1);
            }
        }
        
        if(x<t->data.key)
        {
            if(remove(x,t->left))return true;
            return adjust(t,0);
        }
        else
        {
            if(remove(x,t->right))return true;
            return adjust(t,1);
        }
    }
    
    void clear(AvlNode*t)
    {
        if(t==NULL)return;
        clear(t->left);
        clear(t->right);
        delete t;
        t=NULL;
    }
    
    int height(AvlNode*t)const{return t==NULL?0:t->height;}
    
    void LL(AvlNode*&t)
    {
        AvlNode* t1=t->left;
        t->left=t1->right;
        t1->left=t;
        t->height=max(t->left->height,t->right->height)+1;
        t1->height=max(t1->left->height,t1->right->height)+1;
        t=t1;
    }

    void LR(AvlNode*&t)
    {
        RR(t->left);
        LL(t);
    }
    
    void RL(AvlNode*&t)
    {
        LL(t->right);
        RR(t);
    }
    
    void RR(AvlNode*&t)
    {
        AvlNode* t1=t->right;
        t->right=t1->left;
        t1->left=t;
        t->height=max(t->left->height,t->right->height)+1;
        t1->height=max(t1->left->height,t1->right->height)+1;
        t=t1;
    }
    int max(int a,int b){return (a>b)?a:b;}
    bool adjust(AvlNode*&t,int subTree)//调整树平衡的函数
    {
        if(subTree)
        {
            if(height(t->left)-height(t->right)==1)return true;
            if(height(t->left)==height(t->right)){--t->height;return false;}
            if(height(t->left->right)>height(t->left->left))
            {
                LR(t);
                return false;
            }
            LL(t);
            if(height(t->right)==height(t->left))return false;
            else return true;
        }
        else
        {
            if(height(t->right)-height(t->left)==1)return true;
            if(height(t->right)==height(t->left)){--t->height;return false;}
            if(height(t->right->left)>height(t->right->right))
            {
                RL(t);
                return false;
            }
            RR(t);
            if(height(t->right)==height(t->left))return false;
            else return true;
        }
    }
};
```
私有的remove()函数返回bool类型，false表示需要检查目标节点的父结点的平衡度。
而adjust函数表示对所给结点的五种情况进行处理，如果需要修改父结点，返回false。

### <font color=redblack>红黑树</font>
#### 【定义】
<font color=blackred>$红黑树本质是2-3-4树的BST实现,红结点理解为红链，红链代表同一个结点的两个键值$</font>
红黑树是一棵具有如下特点的二叉查找树。

（1）每个结点都被染成红色或黑色

（2）根节点是黑色的

（3）如果一个结点是红的，那么它的儿子结点必须是黑色的

（4）从任何一个结点出发到空结点（一般认为空结点为黑色结点）的路径上，必须包含相同数目的黑色结点。

<font color=red>性质3、4表明各条路径的长度差是有限的，最长的路径是最短路径的一倍，最长的路径是由红黑结点相间组成的。</font>

#### 【插入操作】
&emsp;&emsp;新结点总是作为叶结点插入，与二叉查找树一致，但是新插入的结点只能是红色（若新插入结点是黑色的，将违反性质4）。若插入结点的父结点是黑色，结束；若为红色，则需要通过``修改结点的着色和旋转``维持红黑树。其主要分为两种情况：
<font color=red>设红结点为X，父结点为P且也是红色，P的父结点为G，且根据红黑树定义G一定为黑色。</font>
1. P的兄弟结点S是黑色的。
   1. X是G的外侧结点。LLb()&&RRb()函数，其中b表示P的兄弟结点是黑色的。对G执行一次单旋转，最后的旋转结果中令根结点为黑色，左右儿子结点为红色。
   2. X是G的内侧结点。LRb()&&RLb()函数。对G执行一次双旋转，并令根节点为黑色，左右儿子结点为红色。
2. P的兄弟结点S是红色的。此时G是黑色，P、S全为红色。将G变成红色，P、S变成黑色，此时问题解决了但没完全解决，因为G的父亲结点可能也是红色，则需要递归向上回溯。
&emsp;&emsp;为了插入过程不再向上回溯，采取自顶向下的处理过程，<font color=blue>若遇到结点t为黑色，左右孩子非空且均为红色，将t结点变成红色，左右孩子变成黑色。</font>通过上述操作保证插入时``只存在``父亲节点为黑色，或者兄弟结点S为黑色两种情况，不会存在回溯。

综上所述，插入的逻辑过程如下：
```
    if 根节点空：创建新树//特判条件

    while true：
        if 结点非空：
            if:左右孩子非空且为红色：
                黑色下沉
                检查一次t，t->paret,t->parent->parent结点是否存在情况1。
            else:
                t结点向下一层
        else:
            插入结点
            insertAdjust(granP,parent,t)
            root->color=BLACK//为满足性质2
```

#### 删除操作


## 图论

### 查找所有强连通分量
#### 实现
1. 从图G任意节点开始执行dfs,并按照遍历的顺序给每个结点编号。
2. 将图G的每条边逆向，从编号最大的结点开始dfs
3. 由此得到的每棵树就是G的强连通分量

### 拓扑排序（AOV网）
#### 定义
将有向无环图按照如下规则排序：如果有一条从u到v的路径，那么结点v在拓扑排序中必须出现在结点u之后。
事件定义在结点上（Acativity on Vers）
#### 实现关键
<font color=whitegreen>类似BFS实现，不同的是，一个节点只有当所有可以到达该节点的其他结点都被访问后才能访问。</font>
关键是使用数组inDegree保存每个结点的入度，并将入度为0的结点压入队列中。

### 关键路径（AOE网）
#### 定义 
1. AOE（Acativity on Edges）关键事件定义在边上，可用于估算工程的完成时间。主要解决完成工程至少需要多少时间以及哪些活动是影响的关键。
2. 最早发生时间：从源点到某个结点的最长路径称为最早发生时间。
3. 最迟发生时间：不推迟整个工程完成的前提下，从顶点出发的最迟开始时间。
4. 时间余量：最迟和最早发生时间之差。
5. 关键路径：从源点到收点的最长路径。该上的活动称之为关键活动
#### 特性
1. 完成工程至少需要的时间对应的是从源点到收点的最长路径的长度。
2. 时间余量之差为0则为关键活动
3. 每个顶点的最早发生时间=max(每个直接前驱最早发生时间+该前驱到该点活动时间)
4. 最迟发生时间=min(每个直接后继最迟发生时间-该点到该后继活动时间)

#### 找出关键路径实现
1. 找出拓扑序列
2. 从头到尾遍历拓扑序列找出最早发生时间
3. 从尾到头遍历拓扑序列找最迟发生时间
4. 从头到尾遍历拓扑序列，找最早发生时间和最迟发生时间相等的顶点，组成关键路径。


### 最小生成树
#### 定义
1. 最小生成树：加权无向连通图众多生成树中权值和最小的生成树。
2. 生成最小生成树的常用算法为：Prim算法和Kruskal算法
#### 特性
1. n个结点的无向连通图最大存在$$\frac{n*(n-1)}{2}$$条边，最小生成树即从中挑出n-1条边使得权值最小并连通所有结点。
2. Kruskal算法适用于稀疏图；Prim算法适用于稠密图。
   
### Kruskal算法
#### 基本思想
从边的角度出发。依次选择图中权值最小的边，若加入这条边不会产生回路，则加入；否则考虑下一条边。直至选择出n-1条边
#### 实现
1. 选择权值最小的边采取优先队列
2. 判读是否产生回路使用并查集确定两个结点是否属于同一棵树

### Prim算法
#### 基本思想
从顶点的角度出发，将一个个结点加入生成树，直到所有结点被加入
#### 实现
1. 初始时，生成树节点集U为空
2. 随意选择一个结点，加入结点集。重复下列工作至U=V
   (1)选择两个端点分别位于U和V-U的代价最小的边(u,v)
   (2)把(u,v)加入生成树边集，v加入U。

### 最短路径问题
#### 定义
1. 单源最短路径：某一个结点到所有结点的最短路径。常用Dijkstra算法
2. 结点对的最短路径：求每一对结点之间的最短路径。常用Floyd算法
### 非加权单源最短路径
1. 算法：
通过BFS算法直到所有结点都找到最短路径即可结束
需要distance数组和prev数组，prev数组记录到达该节点的上一个结点。
输出路径使用递归函数输出prev数组
2. 伪代码
```cpp
unweightedShortDistance(start)
{
    for->distance(v)=无穷大;
    distance[start]=0;
    start->queue;
    while(!queue.empty())
    {
        u=queue.pop();
        for(u的所有邻接点v)
        {
            if(distance[v]==无穷大)
            {
                // 注意：无权值最短路径不需要比较权值大小
                // 因为队列是一层一层来进行遍历的，先访问则代表最短路径。
                distance[v]=distance[u]+1;
                pre[v]=u;
                queue.push(v);
            }
        }
    }

}
```

### Dijkstra算法
本质是BFS加数组不断更新最短路径
+ 带有负权值的图
  <font color=red>注：不能让每条边的权值都增加一个值直到所有的权值都变成正！</font>
  实际解决办法：采取加权图和非加权图的方法结合。在遇到比之前路径更短的结果时，要继续检查后续结点，后续结点同样可能会存在更短的路径。而Dijstra算法遇到更短路径只需要更新即可。

正权图请使用 dijkstra 算法,负权图请使用 SPFA 算法
dijkstra算法还可以通过小根堆优化！
实现代码
```cpp
struct Node{
    int v;
    ll w;
    Node(int x,long long y):v(x),w(y){}
    bool operator < (const Node &A) const {
        return w > A.w;
    }
};
std::vector<std::vector<Node>>map;
bool vis[MAX];
ll dis[MAX];
void init(int n){
    map.resize(n+1);
    for(int i=1;i<=n;++i){vis[i]=false;dis[i]=inf;}
}

/*
1 采用小根堆存储结点，按照到达这个结点的最短路径排序
2 对于小根堆pop出的结点，如果已经访问过了，不需要再访问，因为之前一定有更小的
3 如果没有访问过，并且当前存在更短的路径，push入小根堆
*/

void dijkstra(int s){
    std::priority_queue<Node>q;
    dis[s]=0;
    q.push(Node(s,dis[s]));
    while(!q.empty()){
        Node st=q.top();
        int u=st.v;q.pop();
        // 优化修改点1
        if(vis[u])continue;
        vis[u]=true;

        auto connect=map[u];
        for(Node n:connect){
            int v=n.v;ll w=n.w;
            // 优化修改点2
            if(!vis[v] && dis[u]+w < dis[v]){
                dis[v]=dis[u]+n.w;
                q.push(Node(v,dis[v]));
            }
        }
    }
}
```
### SPFA-BFS算法-判断负环
`判断条件是存在一点入队次数大于等于总顶点数。`
```cpp
bool SPFA(int& st,int n){
    std::stack<int>s; // 一般使用队列而非stack 但是我懒得改了:)

    for(int i=1;i<=n;++i)s.push(i),vis[i]=true;

    while(!s.empty()){
        int u=s.top();s.pop();vis[u]=false;
        auto vec=map[u];
        for(Node x:vec){
            int v=x.v;
            ll w=x.w;
            if(dis[u]+w<dis[v]){
                dis[v]=dis[u]+w; // 如果是判断负环的算法，dis初始化为0效果更好
                In[v]=In[u]+1;
                if(In[v]>=n){st=v;return true;} // bfs判断存在负环的关键
                if(!vis[v]){
                    s.push(v);vis[v]=true;
                }
            }
        }
    }
    return false;
}
```
### SPFA-DFS算法
`判断条件是存在一点在一条路径上出现多次。`
该算法在求出负环上更加有效
```cpp

bool spfa(int u){
  vis[u] = true;
  int i;
  for(i = head[u]; i; i = e[i].next){
    int v = e[i].to, w = e[i].w;
    if(dis[v] > dis[u] + w){
      dis[v] = dis[u] + w;
      if(vis[v]) return false;
      if(!spfa(v)) return false;
    }
  }
  vis[u] = false;
  return true;
}
```
### ST表
它是解决RMQ问题(区间最值问题)的一种强有力的工具

它可以做到O(nlogn)预处理，O(1)查询最值

局限性：对于修改的区间无能为力

#### 算法思想
1. 预处理
   对于st表 ``f[i][j]``
   表示$$f[i][j]=Max[i, i+2^j-1]$$即从i开始，区间长度为2^j的最大值。
   核心是dp的思想。状态转移方程为：
   $$f[i][j]=Max(f[i][j-1],f[i+2^{(j-1)}][j-1])$$
   ``f[i][0]``就是需要处理的数组``a[i]``

2. 查询
   对于查询的区间``[L,R]``:
   $$k=log(R-L+1)$$
   $$Max[L,R]=max(f[L][k],f[R-2^k+1][k])$$
   ``f[L][k]``表示从左端点开始的区间
   推导：
   $$L+2^k-1<=R$$
   $$k<=log(R-L+1)$$
   对于右端点，取相同长度，则起点为：
   $$R-2^k+1$$
3. tips
   求log采取
   ```cpp
   log[0]=-1
   for(int i=1;i<=n;++i)
        log[i]=log[i>>1]+1;
   ```
   求2^k采取
   1<<(k)
4. 模板
```cpp
#include<iostream>
using namespace  std;
const int maxn=1e5+10;
int f[maxn][22];
int log2[maxn];
inline int read()
{
    char c=getchar();int x=0,f=1;
    while(c<'0'||c>'9'){if(c=='-')f=-1;c=getchar();}
    while(c>='0'&&c<='9'){x=x*10+c-'0';c=getchar();}
    return x*f;
}
void init()
{
    log2[0]=-1;
    for(int i=1;i<=maxn;++i)
        log2[i]=log2[i/2]+1;
}
int query(int l,int r)
{
    int k=log2[r-l+1];
    return max(f[l][k],f[r-(1<<k)+1][k]);
}
int max(int a,int b){return (a>b)?a:b;}
int main(){
    int n=read(),m=read();
    init();
    for(int i=1;i<=n;++i)f[i][0]=read();
    for(int j=1;j<=21;++j)
        for(int i=1;i+(1<<j)-1<=n;++i)
            f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
    for(int i=1;i<=m;++i)
    {
        int l=read(),r=read();
        printf("%d\n",query(l,r));
    }
    return 0;
}
```
### 树注意事项
1. 对于N个结点，数组可以开tree[N<<1]；

### 网络流问题
#### 最大网络流问题
Dinic算法<br />
&emsp;&emsp;首先定义增广路：<font color = green>从源点到汇点上所有边的残余容量>0的路径为增广路。</font>
&emsp;&emsp;Dinic算法本质上就是先通过bfs找出`分层图`（即获得源点到每一个结点的深度），利用分层图进行dfs搜索`增广路`（即只选择深度为该节点深度+1的结点进行增广），分层图可以确保我们找到的增广路是最短的，并不去寻找相同深度的其他结点。对于找到的增广路，减去边上的容量，并增加反向边（提供返回的机会，否则当一条路走过但还存在更优秀的走法，反向边可以提供反悔的路径）
&emsp;&emsp;优化：<br />
1. 当前弧优化：如果一条边已经被增广过，那么它就没有可能被增广第二次。那么，我们下一次进行增广的时候，就可以不必再走那些已经被增广过的边。
2. 多路增广优化：每次找到一条增广路的时候，如果残余流量没有用完我们可以利用残余部分流量，再找出一条增广路。这样就可以在一次 DFS 中找出多条增广路，大大提高了算法的效率。

算法复杂度为O（n^2m）
示例代码：
```cpp
class Maxflow {
    struct Edge {
        int to, w, nxt;
    } e[MAXM << 1];// 注意要留出反向边的位置
    int src, sink, cnt; // 源点，汇点，边的计数变量
    int head[MAXN];
    int cur[MAXN];// 弧优化数组
    int depth[MAXN];
    int n, m;
public:
    void add(int u, int v, int w) {
        e[++cnt] = {v, w, head[u]};
        head[u] = cnt;

        // 反向边
        e[++cnt] = {u, 0, head[v]};
        head[v] = cnt;
    }

    void init(int s, int t, int _n, int _m) {
        src = s, sink = t;
        n = _n, m = _m;
        cnt = 1;
        for (int i = 1; i <= n; ++i) {
            head[i] = -1;
        }

        for (int i = 1; i <= m; ++i) {
            int u, v, w;
            u = read(), v = read(), w = read();
            add(u, v, w);
        }
    }

    bool updateDepth()// bfs
    {
        for (int i = 1; i <= n; ++i)depth[i] = -1;
        queue<int> q;
        q.push(src);
        depth[src] = 1;
        while (!q.empty()) {
            int top = q.front();
            q.pop();
            for (int i = head[top]; ~i; i = e[i].nxt) {
                // 由于队列的FIFO特性，最先访问的应该是最短路径
                if (e[i].w && depth[e[i].to]==-1) {
                    q.push(e[i].to);
                    depth[e[i].to] = depth[top] + 1;
                }
            }
        }
        return depth[sink] != -1;
    }

    int dfs(int u, int delta) {
        if (u == sink)return delta;
        int ret = 0;
        for (int i = cur[u]; ~i; i = e[i].nxt) {
            cur[u] = i;
            if (e[i].w && depth[e[i].to] == depth[u] + 1) {
                int final = dfs(e[i].to, min(delta, e[i].w));
                if (final > 0) {
                    e[i].w -= final;
                    e[i ^ 1].w += final;
                    delta -= final;
                    ret += final;
                } else depth[e[i].to] = -1;
            }
        }
        return ret;
    }

    int maxflow() {
        int ans = 0;
        while (updateDepth()) {
            for (int i = 1; i <= n; ++i)
                cur[i] = head[i];
            ans += dfs(src, inf);
        }
        return ans;
    }
};
```

### tarjan算法
在有向图G中，如果两个顶点间至少存在一条路径，称两个顶点强连通(strongly connected)。如果有向图G的每两个顶点都强连通，称G是一个强连通图。非强连通图有向图的极大强连通子图，称为强连通分量(strongly connected components)。

直接根据定义，用双向遍历取交集的方法求强连通分量，时间复杂度为O(N^2+M)。更好的方法是Kosaraju算法或Tarjan算法，两者的时间复杂度都是O(N+M)。

[Tarjan算法]
Tarjan算法是基于对图深度优先搜索的算法，每个强连通分量为搜索树中的一棵子树。搜索时，把当前搜索树中未处理的节点加入一个堆栈，回溯时可以判断栈顶到栈中的节点是否为一个强连通分量。

定义DFN(u)为节点u搜索的次序编号(时间戳)，Low(u)为u或u的子树能够追溯到的最早的栈中节点的次序号。由定义可以得出:
```cpp
Low(u)=Min
{
    DFN(u),
    Low(v),(u,v)为树枝边，u为v的父节点
    DFN(v),(u,v)为指向栈中节点的后向边(非横叉边)
}
```
当DFN(u)=Low(u)时，以u为根的搜索子树上所有节点是一个强连通分量。

算法伪代码如下
```cpp
tarjan(u)
{
	DFN[u]=Low[u]=++Index                      // 为节点u设定次序编号和Low初值
	Stack.push(u)                              // 将节点u压入栈中
	for each (u, v) in E                       // 枚举每一条边
		if (v is not visted)               // 如果节点v未被访问过
			tarjan(v)                  // 继续向下找
			Low[u] = min(Low[u], Low[v])
		else if (v in S)                   // 如果节点v还在栈内
			Low[u] = min(Low[u], DFN[v])
	if (DFN[u] == Low[u])                      // 如果节点u是强连通分量的根
		repeat
			v = S.pop                  // 将v退栈，为该强连通分量中一个顶点
			print v
		until (u== v)
}
```
可以发现，运行Tarjan算法的过程中，每个顶点都被访问了一次，且只进出了一次堆栈，每条边也只被访问了一次，所以该算法的时间复杂度为O(N+M)。<br />
&emsp;&emsp;求有向图的强连通分量还有一个强有力的算法，为`Kosaraju`算法。Kosaraju是基于对有向图及其逆图两次DFS的方法，其时间复杂度也是O(N+M)。与Trajan算法相比，Kosaraju算法可能会稍微更直观一些。但是Tarjan只用对原图进行一次DFS，不用建立逆图，更简洁。在实际的测试中，Tarjan算法的运行效率也比Kosaraju算法高30%左右。此外，该Tarjan算法与求无向图的双连通分量(割点、桥)的Tarjan算法也有着很深的联系。学习该Tarjan算法，也有助于深入理解求双连通分量的Tarjan算法，两者可以类比、组合理解。

tarjan算法的C++程序
```cpp
void tarjan(int i)
{
	int j;
	DFN[i]=LOW[i]=++Dindex;
	instack[i]=true;
	Stap[++Stop]=i;
	for (edge *e=V[i];e;e=e->next)
	{
		j=e->t;
		if (!DFN[j])
		{
			tarjan(j);
			if (LOW[j]<LOW[i])
				LOW[i]=LOW[j];
		}
		else if (instack[j] && DFN[j]<LOW[i])
			LOW[i]=DFN[j];
	}
	if (DFN[i]==LOW[i])
	{
		Bcnt++;
		do
		{
			j=Stap[Stop--];
			instack[j]=false;
			Belong[j]=Bcnt;
		}
		while (j!=i);
	}
}
void solve()
{
	int i;
	Stop=Bcnt=Dindex=0;
	memset(DFN,0,sizeof(DFN));
	for (i=1;i<=N;i++)
		if (!DFN[i])
			tarjan(i);
}
```

### 2-SAT算法
#### tarjan算法
&emsp;&emsp;该算法用于判断图中是否存在强连通图，并产生一个Belong数组，下标i对应结点编号，Belong[i]表示归属的集合编号，编号越小表示拓扑排序越大，即优先被访问到。
### 2-SAT
&emsp;&emsp;`定义`：2-SAT，简单的说就是给出n个集合，每个集合有两个元素，已知若干个关系<a, b>，表示a与b矛盾（其中a与b属于不同的集合）。然后从每个集合选择一个元素，判断能否一共选择个两两不矛盾的元素。显然可能有多种选择方案，一般题中只需要求出一种即可。<br />
&emsp;&emsp;`解决办法`：假设有 a1,a2 和 b1,b2 两对，已知 a1 和 b2 间有矛盾，于是为了方案自洽，由于两者中必须选一个，所以我们就要拉两条有向边(a1, b1)和(b2, a2)表示选了a1则必须选b1，选了b2则必须选a2才能够自洽。

然后通过这样子建边我们跑一遍 Tarjan SCC 判断是否有一个集合中的两个元素在同一个 SCC 中，若有则输出不可能，否则输出方案。构造方案只需要把几个不矛盾的 SCC 拼起来就好了。

输出方案时可以通过变量在图中的拓扑序确定该变量的取值。`复杂度为O(n+m)`
### bfs（剪枝优化）
```cpp
struct Node{
    int v,cost;
    bool operator < (Node obj)const{return cost > obj.v;}
}
priority_queue<Node>q;
void bfs(int s){
    q.push({s,0});
    while(!q.empty())
    {
        // 注意：！！！！
        // vis[top]的判断一定要放在里面，因为单调队列的缘故，
        // 只有到实际取出来的时候，获得的才是最大值，如果在入队的时候
        // 使用vis[]卡住入队是不正确的！
        auto top = q.top();q.pop();
        if(vis[top])continue;
        vis[top]=true;
        H[top.v] = top.cost;
        for(int i=head[top.v];~i;i=edge[i].nxt)
        {
            q.push({edge[i].v,edge[i].w+top.cost});
        }
    }
}
```
### k短路
#### A*算法
&emsp;&emsp;A*算法定义了一个对当前状态x的估价函数 ，其中f(x) = g(x)+h(x)为从初始状态到达当前状态的实际代价,h(x)为从当前状态到达目标状态的最佳路径的估计代价。每次取出f(x)最优的状态x，扩展其所有子状态，可以用 `优先队列` 来维护这个值。
<br />
&emsp;&emsp;在求解k短路问题时，令h(x)为从当前结点到达终点t的最短路径长度。可以通过在反向图上对该结点跑单源最短路预处理出对每个结点的这个值。
由于设计的距离函数和估价函数，对于每个状态需要记录两个值，为当前到达的结点g(x)和已经走过的距离f(x)。<br />
开始我们将初始状态 s 加入优先队列。每次我们取出估价函数 f(x) 最小的一个状态，枚举该状态到达的结点的所有出边，将对应的子状态加入优先队列。当我们访问到一个结点第 k 次时，对应的状态的就是从 s 到该结点的第 k 短路。
<br />
优化：由于只需要求出从初始结点到目标结点的第k短路，所以已经取出的状态到达一个结点的次数大于k次时，可以不扩展其子状态。因为之前k次已经形成了k条合法路径，当前状态不会影响到最后的答案。

当图的形态是一个n元环的时候，该算法最坏是$$O(nk\log{n})$$的。但是这种算法可以在相同的复杂度内求出从起始点s到每个结点的前k短路。
```cpp
void generateH(int start) {
    q2.push({start, 0});
    // 使用优先队列的时候，vis不能放在入队的时候判断！
    while (!q2.empty()) {
        auto top = q2.top();
        q2.pop();
        if (vis[top.x])continue;
        vis[top.x] = true;
        H[top.x] = top.cost;
        for (int i = h2[top.x]; ~i; i = e2[i].nxt) {
            int to = e2[i].to;
            q2.push({to, top.cost + e2[i].w});
        }
    }

}

void bfs(int s, int k, int t) {
    q1.push({s, 0});
    while (!q1.empty()) {
        Node1 u = q1.top();
        ll cost = u.cost;
        q1.pop();

        if (cnt[u.x] + 1 > k || cost == ans[cnt[u.x]][u.x])continue;

        ++cnt[u.x];
        ans[cnt[u.x]][u.x] = cost;
        if (u.x == t && cnt[u.x] == k) return;
        for (int i = h1[u.x]; ~i; i = e1[i].nxt)
            q1.push({e1[i].to, cost + e1[i].w});
    }
}


int main() {
    int n, m, s, t, k, u, v;
    ll w;
    n = read(), m = read(), s = read(), t = read(), k = read();
    init(n, k, t);
    for (int i = 0; i < m; ++i) {
        u = read(), v = read(), w = read();
        add1(u, v, w);
        add2(v, u, w);
    }
    generateH(t);
    bfs(s, k, t);
    for (int i = 1; i <= k; ++i)
        printf("%ld ", ans[i][t]);
    return 0;
}
```
### 链式前向星
初始化
```cpp
void init(int n){
    for(int& i:head){
        i = -1;
    }
}
```
存边
```cpp
void add(int u,int v,int w)
{
    edge[cnt].w = w;
    edge[cnt].to = v;
    edge[cnt].next = head[u];
    head[u] = cnt++;
}
```
遍历
```cpp
for(int i = head[u];~i;i=edge[i].nxt)
{
    ...
}
```
## 快读
```cpp
inline int read()
{
    char c=getchar();int x=0,f=1;
    while(c<'0'||c>'9'){if(c=='-')f=-1;c=getchar();}
    while(c>='0'&&c<='9'){x=x*10+c-'0';c=getchar();}
    return x*f;
}
```
## 树状数组
log(n)单点修改,log(n)区间和查询
```cpp
template<typename T>
struct BIT{
#ifndef lowbit
#define lowbit(x) (x & (-x));
#endif
    static const int maxn = 1e3+50;
    int n;
    T t[maxn];

    BIT<T> () {}
    BIT<T> (int _n): n(_n) { memset(t, 0, sizeof(t)); }
    BIT<T> (int _n, T *a): n(_n) {
        memset(t, 0, sizeof(t));
        /* 从 1 开始 */
        for (int i = 1; i <= n; ++ i){
            t[i] += a[i];
            int j = i + lowbit(i);
            if (j <= n) t[j] += t[i];
        }
    }

    void add(int i, T x){
        while (i <= n){
            t[i] += x;
            i += lowbit(i);
        }
    }

    /* 1-index */
    T sum(int i){
        T ans = 0;
        while (i > 0){
            ans += t[i];
            i -= lowbit(i);
        }
        return ans;
    }
    /* 1-index [l, r] */
    T sum(int i, int j){
        return sum(j) - sum(i - 1);
    }
/*
href: https://mingshan.fun/2019/11/29/binary-indexed-tree/
note:
    C[i] --> [i - lowbit(i) + 1, i]
    father of i --> i + lowbit(i)
    node number of i --> lowbit(i)
*/
};
```
## 快排
```cpp
void qsort(int l,int r)//应用二分思想{
    int mid=a[(l+r)/2];//中间数
    int i=l,j=r;
    do{
        while(a[i]<mid) i++;//查找左半部分比中间数大的数
        while(a[j]>mid) j--;//查找右半部分比中间数小的数
        if(i<=j)//如果有一组不满足排序条件（左小右大）的数
        {
            swap(a[i],a[j]);//交换
            i++;
            j--;
        }
    }while(i<=j);//这里注意要有=
    if(l<j) qsort(l,j);//递归搜索左半部分
    if(i<r) qsort(i,r);//递归搜索右半部分
}

```