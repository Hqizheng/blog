## 字符串算法总结
清明时节雨纷纷，题中字符串欲断魂（
#### 字符串hash
众所周知，hash是一种很玄学的算法，而字符串hash自然也是了（手动滑稽，希望hash killer 3没事）
字符串hash也是通过一定的变换求出hash函数值后比对等，可用std::map维护，不过时间复杂度为O(logn)，不如手写的O(1)（大多数情况）
hash有几种常用的方法，即单模hash，双模hash，挂链hash等。
##### 单模hash
可以通过自然溢出的技巧使其省去取模操作，代码如下
```cpp
    char str[30];
    std::cin>>str+1;
    int ls=std::n(str+1);//防止每次循环都要计算一遍
    s[0]=0;
    for(int i=1;i<=ls;i++) s[i]=s[i-1]*SEED+(str[i]-'0'); 
    //既保存为SEED进制，SEED常用13131或1313131之类,也可用不同质数
```
还有在O(1)取出子串hash值的代码
```cpp
void init() {
    p[0]=1;
    for(int i=1;i<=ls;i++) p[i]=p[i-1]*SEED;
}

int gethash(int a,int b) {
    return s[b]-s[a-1]*p[b-a+1];
}
```
##### 挂链
码不好写，咕了（
##### 双模
改一改单模不就出来了嘛
#### KMP算法
KMP算法是一种很神奇的字符串匹配算法,代码比较好写，下面就是@lnzwz的板子啦
```cpp
int KMP(char S[200010],char T[200010],int wz[200010],int n,int m)
{
	ne[0]=-1;
	for(int i=1;i<=m;i++)
	{
		int t=ne[i-1];
		while(t!=-1&&T[i-1]!=T[t])
			t=ne[t];
		ne[i]=t+1;
	}
	int a=0,b=0,sl=0;
	while(a<=n)
	{
		if(b==m)
		{
			wz[sl++]=a-m;
			b=ne[b];
		}
		if(b==-1||S[a]==T[b])
			a+=1,b+=1;
		else
			b=ne[b];
	}
	return sl;
}
```
#### trie树
很简单的一种实现字符串记录与查找的一种树形结构，板子自然也很好写啦(
```cpp
void bfs()
{
	int he=0,ta=1;
	dl[0]=0;
	while(he<ta)
	{
		int u=dl[he];
		if(u==0||fa[u]==0)
			fi[u]=0;
		else
			fi[u]=trs[fi[fa[u]]][fb[u]];
		for(int i=0;i<94;i++)
		{
			if(trs[u][i])
				dl[ta++]=trs[u][i];
			else
				trs[u][i]=trs[fi[u]][i];
		}
		he+=1;
	}
}
```
#### AC自动机
###### 不是自动AC机啦
也不难，板子大致上就长成这样子
```cpp
int trie[maxn][26]; //字典树
int cntword[maxn];  //记录该单词出现次数
int fail[maxn];     //失败时的回溯指针
int cnt = 0;

void insertWords(string s){
    int root = 0;
    for(int i=0;i<s.size();i++){
        int next = s[i] - 'a';
        if(!trie[root][next])
            trie[root][next] = ++cnt;
        root = trie[root][next];
    }
    cntword[root]++;      //当前节点单词数+1
}
void getFail(){
    queue <int>q;
    for(int i=0;i<26;i++){      //将第二层所有出现了的字母扔进队列
        if(trie[0][i]){
            fail[trie[0][i]] = 0;
            q.push(trie[0][i]);
        }
    }

//fail[now]    ->当前节点now的失败指针指向的地方
tire[now][i] -> 下一个字母为i+'a'的节点的下标为tire[now][i]
    while(!q.empty()){
        int now = q.front();
        q.pop();

        for(int i=0;i<26;i++){      //查询26个字母
            if(trie[now][i]){
                //如果有这个子节点为字母i+'a',则
//让这个节点的失败指针指向(((他父亲节点)的失败指针所指向的那个节点)的下一个节点)
                //有点绕,为了方便理解特意加了括号

                fail[trie[now][i]] = trie[fail[now]][i];
                q.push(trie[now][i]);
            }
            else//否则就让当前节点的这个子节点
                //指向当前节点fail指针的这个子节点
                trie[now][i] = trie[fail[now]][i];
        }
    }
}


int query(string s){
    int now = 0,ans = 0;
    for(int i=0;i<s.size();i++){    //遍历文本串
        now = trie[now][s[i]-'a'];  //从s[i]点开始寻找
        for(int j=now;j && cntword[j]!=-1;j=fail[j]){
            //一直向下寻找,直到匹配失败(失败指针指向根或者当前节点已找过).
            ans += cntword[j];
            cntword[j] = -1;    //将遍历国后的节点标记,防止重复计算
        }
    }
    return ans;
}

int main() {
    int n;
    string s;
    cin >> n;
    for(int i=0;i<n;i++){
        cin >> s ;
        insertWords(s);
    }
    fail[0] = 0;
    getFail();
    cin >> s ;
    cout << query(s) << endl;
    return 0;
}
```
