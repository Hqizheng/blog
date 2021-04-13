#### [洛谷P1896SCOI2005互不侵犯]
状压DP大水题，注意状压的数据范围能开ll就不int
代码：
```cpp
#include<bits/stdc++.h>
#define ll long long

int sit[2000],gs[2000];
int cnt=0;
int n,yong;
ll dp[10][2000][100]={0};

void dfs(int he,int sum,int node) {
    if(node>=n) {
        sit[++cnt]=he;
        gs[cnt]=sum;
        return;
    }
    dfs(he,sum,node+1);
    dfs(he+(1<<node),sum+1,node+2);
}

int main() {
    std::cin>>n>>yong;
    dfs(0,0,0);
    for(int i=1;i<=cnt;i++) {
    	dp[1][i][gs[i]]=1;
    }
    for(int i=2;i<=n;i++)
        for(int j=1;j<=cnt;j++)
            for(int k=1;k<=cnt;k++) {
                if(sit[j]&sit[k]||(sit[j]<<1)&sit[k]||sit[j]&(sit[k]<<1)) continue;
                for(int s=yong;s>=gs[j];s--)
                	dp[i][j][s]+=dp[i-1][k][s-gs[j]];
                }

    ll ans=0;
    for(int i=1;i<=cnt;i++)ans+=dp[n][i][yong];
    std::cout<<ans<<std::endl;
    return 0;
}
```
