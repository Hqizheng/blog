### 可并堆
#### 一。概述
可并堆，是一种支持合并操作的堆，竞赛中常用的主要是左偏树，其他还有斜堆，二项堆，配对堆，斐波那契堆等。本文主要讲左偏树。
#### 二.相关定义
外节点：只有一个儿子或没有儿子的节点     
距离：一个节点到离它最近的外节点的距离，即两节点之间路径的权值和，特别地，外节点的距离为零，空节点的距离为-1     
左偏性质：左儿子的距离大于右儿子的距离   
左偏树：一种满足左偏性质的堆有序二叉树   
左偏树的距离：该左偏树根节点的距离  
#### 三.一些性质
堆的性质  
任一节点距离等于其右儿子的距离加一  
n个节点的左偏树根节点距离不超过ln n
对于一棵距离为d的左偏树，节点数不少于2^(k+1)−1  
#### 四.节点信息（一般情况）
val：权值  
lson：左儿子  
rson：右儿子  
dist：距离  
father：父亲  
#### 四.基本操作
##### 合并
```cpp
int Merge(int x,int y) {
    if(!x || !y)
        return x+y;
    if(v(x)>v(y) ||(v(x)==v(y) && x>y))
        swap(x,y);
    int &ls=l(x),&rs=r(x);
    rs=Merge(rs,y);
    f(rs)=x;
    if(d(ls)<d(rs))
        swap(ls,rs);
    d(x)=d(rs)+1;
    return x;
}
```
##### 删除根节点
```cpp
void Delroot(int x) {
    int ls=l(x),rs=r(x);
    v(x)=-1,f(ls)=0,f(rs)=0;
    Merge(ls,rs);
}
```
##### 删除任意节点
```cpp
void Delete(int x) {
    int fx=f(x),p=Merge(l(x),r(x));
    int &ls=l(fx),&rs=r(fx);
    f(p)=fx;
    ls==x?ls=p:rs=p;
    while(p) {
        if(d(ls)<d(rs))
            swap(ls,rs);
        if(d(fx)==d(rs)+1)
            return ;
        d(fx)=d(rs)+1;
        p=fx,fx=f(x);
        ls=l(fx),rs=r(fx);
    }
}
```
##### 建树
```cpp
void Build() {
    queue<int> q;
    for(int i=1;i<=n;i++)
        q.push(i);
    int x,y;
    while(q.size()) {
        x=q.front();q.pop();
        y=q.front();q.pop();
        q.push(Merge(x,y));
    }
}
```
————板子来源：https://www.cnblogs.com/TEoS/p/11351372.html
