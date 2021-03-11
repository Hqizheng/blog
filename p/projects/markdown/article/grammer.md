
### 零.概要
线段树是一种十分优秀的数据结构，可用于维护大量的（几乎所有）数列区间问题，而可持久化线段树就是拥有版本记录与回退功能的线段树。具体的一种实现方法为主席树。本文主要讲解主席树。
例题：[P3834 【模板】可持久化线段树 2（主席树）](https://www.luogu.com.cn/problem/P3834)
主席树又名函数式线段树，由HJT学长发明，由于与某位大神姓名首字母一样故称主席树。
主席树的本质是一棵每个节点都是线段树的线段树（注意：是每个节点而不是每棵子树，否则将为普通线段树）。

### 一.建树
说起树状数据结构，第一步当然要说建树啦！首先我们要建一棵空的线段树，即最原始的主席树，此时该树有且只有一个空节点（根节点rt[0]）,再将原序列加入相应位置（实际上和线段树的建树一毛一样啦）
### 二.更新
显然在更新一个叶节点时只有从根节点到该叶节点的一条树链发生变化,那么我们可以直接将不发生改变的子树指向上一个版本的线段树，由此可以达到大大减少时空复杂度的目的。
> 每个主席树的节点即每棵线段树的结构完全相同，只是对应信息（可以理解为线段树的结构完全一样，只是对应叶子节点取值不同，从而有些节点的信息不同，本质是节点不同），此时可以利用历史状态，即利用相邻的上一棵线段树的信息。相邻两颗线段树只有当前待处理的元素不同，其余位置完全一样。因此，如果待处理的元素进入线段树的左子树的话，右子树是完全一样的，可以共用，即直接让当前线段树节点的右子树指向相邻的上一棵线段树的右子树；若进入右子树，情况可以类比。——来自[weixin_30349597的博客](https://blog.csdn.net/weixin_30349597/article/details/95306805)
### 三.查询
我们使用rt[i]表示处理完前i个数之后形成的线段树，可知rt[i]具有前缀和的性质，故可以使用rt[r]-rt[l-1]表示所处理的[l,r]区间。当我们想查询区间[1,3]的时候，求出rt[3]-rt[0]即可。
> 这样我们得到区间[l, r]的数要查询第k大便很容易了，设左节点中存的个数为cnt，当k<=cnt时，我们直接查询左儿子中第k小的数即可，如果k>cnt，我们只要去查右儿子中第k-cnt小的数即可，这边是一道很简单的线段树了。就如查找[1, 3]的第2小数（图上为了方便，重新给节点标号），从根节点1向下搜，发现左儿子2的个数为1,1<2，所有去右儿子3中搜第2-1级第1小的数，然后再往下搜，发现左儿子6便可以了，此时已经搜到底端，所以直接返回节点6维护的值3即可就可以了。——还是来自上面的博客
### 四.代码
```cpp
 1 //主席树.cpp
 2 #include<bits/stdc++.h>
 3 #include<ext/pb_ds/priority_queue.hpp>
 4 
 5 #define N 100000 + 5;
 6 
 7 int a[N], b[N], rt[N * 20], ls[N * 20], rs[N * 20], sum[N * 20];
 8 
 9 int n, k, tot, sz, ql, qr, x, q, T;
10 
11 void Build(int& o, int l, int r) {
12     o = ++ tot;
13     sum[o] = 0;
14     if(l == r) return;
15     int m = (l + r) >> 1;
16     Build(ls[o], l, m);
17     Build(rs[o], m + 1, r);
18 }
19 
20 void update(int& o, int l, int r, int last, int p) {
21     o = ++ tot;
22     ls[o] = ls[last];
23     rs[o] = rs[last];
24     sum[o] = sum[last] + 1;
25     if(l == r) return;
26     int m = (l + r) >> 1;
27     if(p <= m)  update(ls[o], l, m, ls[last], p);
28     else update(rs[o], m + 1, r, rs[last], p);
29 }
30 
31 int query(int ss, int tt, int l, int r, int k) {
32     if(l == r) return l;
33     int m = (l + r) >> 1;
34     int cnt = sum[ls[tt]] - sum[ls[ss]];
35     if(k <= cnt) return query(ls[ss], ls[tt], l, m, k);
36     else return query(rs[ss], rs[tt], m + 1, r, k - cnt);
37 }
38 
39 void work() {
40     std::cin>>ql>>qr>>x;
41     int ans = query(rt[ql - 1], rt[qr], 1, sz, x);
42     printf("%d\n", b[ans]);
43 }
44 
45 int main() {
46     scanf("%d", &T);
47     while(T--) {
48         std::cin>>n>>q;
49         for(int i = 1; i <= n; i ++) std::cin>>a[i],b[i] = a[i];
50         std::sort(b + 1, b + n + 1);
51         sz = std::unique(b + 1, b + n + 1) - (b + 1);
52         tot = 0;
53         Build(rt[0],1, sz);
54         //for(int i = 0; i <= 4 * n; i ++)printf("%d,rt =  %d,ls =  %d, rs = %d, sum = %d\n", i, rt[i], ls[i], rs[i], sum[i]);
55         for(int i = 1; i <= n; i ++)a[i] = lower_bound(b + 1, b + sz + 1, a[i]) - b;
56         for(int i = 1; i <= n; i ++)update(rt[i], 1, sz, rt[i - 1], a[i]);
57         for(int i = 0; i <= 5 * n; i ++)printf("%d,rt =  %d,ls =  %d, rs = %d, sum = %d\n", i, rt[i], ls[i], rs[i], sum[i]);
58         while(q --)work();
59     }
60     return 0;
61 }
```
亦改自上述博客(
### 四.时空复杂度分析及总结
|     | 建树 |更新|查询|
|---- | ---- | ---- | ---- |
|时间复杂度  |   O（nlogn)   |  O(logn)    |   O（logn)  |
空间复杂度窝不会(
不过贼大，这也是主席树最大的缺点。
 
