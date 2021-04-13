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
