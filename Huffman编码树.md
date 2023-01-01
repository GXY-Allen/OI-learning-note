# Huffman编码树

这是一道模板题/水题，但是我这个蒟蒻还是决定将这道题作为我的第一篇题解

## 题目

> ### 题目描述
>
> 构造一个具有n个外部节点的扩充二叉树，每个外部节点Ki有一个Wi对应，作为该外部节点的权。使得这个扩充二叉树的叶节点带权外部路径长度总和最小：
>
> ​                   Min( W1 * L1 + W2 * L2 + W3 * L3 + … + Wn * Ln)
>
> Wi:每个节点的权值。
>
> Li:根节点到第i个外部叶子节点的距离。
>
> 编程计算最小外部路径长度总和。
>
> ### 输入
>
> 第一行输入一个整数n，外部节点的个数。第二行输入n个整数，代表各个外部节点的权值。
> 2<=N<=100
>
> ### 输出
>
> 输出最小外部路径长度总和。
>
> ### 样例输入
>
> ```cpp
> 4
> 1 1 3 5
> ```
>
> ### 样例输出
>
> ```cpp
> 17
> ```

## 思路部分

一看到这题目，显而易见是一道哈夫曼树的模板题

### 什么是哈夫曼树

这时我们需要解决的第一问题。

如题目所言，我们需要构造出一棵二叉树，使这棵树的所有叶子节点的权值为输入的数，然后将每一个叶子节点的权值乘以该叶子节点的深度（与根节点的距离）最后使得所有乘积的和最小。

### 如何构建哈夫曼树

~~显而易见的~~贪心，我们知道使权值越大的点要离根节点越近，权值越小相对离根节点更远，这样的情况显然会更优。

#### 这是具体的操作方法

 **1、初始状态下共有n个结点，结点的权值分别是给定的n个数，将他们视作n棵只有根结点的树。**
 **2、合并其中根结点权值最小的两棵树，生成这两棵树的父结点，权值为这两个根结点的权值之和，这样树的数量就减少了一个。**
 **3、重复操作2，直到只剩下一棵树为止，这棵树就是哈夫曼树。**

于是乎，我们就有了非常明确的思路来写代码

### 上代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int n,ca;
long long sum=0;
struct point{
    int num;
    int w;
    int fa;
    int l;
    int r;
    friend bool operator > (point k,point e){
        return k.w>e.w;
    }
};
point a[300];
priority_queue<point,vector<point>,greater<point> > q;
int len(point nu){
    int length=0;
    while(nu.num!=q.top().num){
        length++;
        nu=a[nu.fa];
    }
    return length;
}
int main(){
    scanf("%d",&n);
    point temp1;
    for(int i=1;i<=n;i++){
        scanf("%d",&a[i].w);
        a[i].num=i;
        a[i].l=a[i].r=a[i].fa=0;
        q.push(a[i]);
    }
    point t1,t2,nw;
    int cnt=0,T=n-1;
    while(T--){
        cnt++;
        t1=q.top();
        q.pop();
        t2=q.top();
        q.pop();
        nw.w=t1.w+t2.w;
        nw.l=t1.num;
        nw.r=t2.num;
        nw.num=cnt+n;
        q.push(nw);
        a[t1.num].fa=nw.num;
        a[t2.num].fa=nw.num;
        a[nw.num]=nw;
    }
    for(int i=1;i<=n;i++){
        sum+=len(a[i])*a[i].w;
    }
    printf("%ld",sum);
    return 0;
}
```

### 然后我们一步一步来看

我们的目标是构建一棵树那么我们要有点，于是我用了结构体

```cpp
struct point{
    int num;
    int w;
    int fa;
    int l;
    int r;
    friend bool operator > (point k,point e){
        return k.w>e.w;
    }
};
```

变量num代表这个点的序号，便于我们之后将它放进优先队列里，w代表权值，fa代表节点的父节点，l和r代表节点的左右儿子，并且我们重载符号">"，方便我们把结构体存入优先队列中。

接下来我们就要模拟这个过程来构造出这棵二叉树

我们将所有的点存入优先队列后，就可以从对头按次序取出最小的两个点，然后将它们合并，形成他们的父节点，并且把父节点重新放入先前的优先队列中，那么究竟我们会加入多少个新点来构成这棵树呢？答案是n-1个新点；

> 证明：
> 每两个点会合成一个新的点，最后会合成根节点
> 所以每次相当于两个点变为一个点
> 最后还剩一个点
> 所以得出我们一共需要新建n-1个点

```cpp
point t1,t2,nw;
int cnt=0,T=n-1;
while(T--){
    cnt++;
    t1=q.top();
    q.pop();
    t2=q.top();
    q.pop();
    nw.w=t1.w+t2.w;
    nw.l=t1.num;
    nw.r=t2.num;
    nw.num=cnt+n;
    q.push(nw);
    a[t1.num].fa=nw.num;
    a[t2.num].fa=nw.num;
    a[nw.num]=nw;
}
```

于是乎我们就在a数组里面得到了哈夫曼树。最后，我们要求得答案，就是将每个点的权值乘上他们各自的深度。

```cpp
int len(point nu){
    int length=0;
    while(nu.num!=q.top().num){
        length++;
        nu=a[nu.fa];
    }
    return length;
}
```

我写了一个思想类似于并查集的小函数，来实现这个功能。即通过一直访问其父节点，并统计次数，以此来获得深度

需要注意的一点是我们在经过n-1次建立新节点后，最后一次根节点会留在优先队列里，所以后面判断是否到达根节点时直接访问优先队列的队首即可。

### 后话

狸谱啊梨谱，这么简单的题我竟然做这么复杂……这道题竟然如此简单

#### 上代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int n,ca;
long long sum=0;
priority_queue<int,vector<int>,greater<int> > q;
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d",&ca);
        q.push(ca);
    }
    int a,b;
    while(q.size()>1){
        a=q.top();
        q.pop();
        b=q.top();
        q.pop();
        sum+=a+b;
        q.push(a+b);
    }
    printf("%d",sum);
    return 0;
}
```

为什么会这么简单啊？我不梨解。

其时我们答案中所乘以的深度其实可以看成我们将这个权值累加了多少次，这样来说，我们要得到答案，就可以将它累加那么多次就可以了，我们发现在每次构建新的父节点是，其实就是把这两个子节点的深度加上了一，这样一来，我们每次构成父节点时就可以同时统计答案，这样一来，我们都不需要结构体来存储这颗二叉树了。

这怎么能这么简单，让我白白写了那么多啊

### 注：此题解为本人第一篇，望各位大神理解
