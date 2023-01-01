# 塔防（cover）

> ### 题目背景
>
> 在某个塔防游戏中，有一种防御塔，可以攻击到上下左右四个方向以及自身位置的敌人。
>
> ### 题目描述
>
> 塔防游戏的⼀个关卡地图可以看作⼀个 的矩阵，也就是 ⾏， 列的矩阵。 其中，⽤ # 代表障碍，⽤ . 代表地⾯。对于每个地⾯，都可以选择不放置防御塔，或者放置防御 塔。每个防御塔能够攻击到⾃⼰所在位置的地⾯，同时，能攻击到⾃⼰位置上下左右四个射线⽅向上的 敌⼈，攻击范围沿射线⽅向延伸。但是射线上如果有其他防御塔或者障碍，将会阻断攻击范围的延伸。 现在，NSH决定随机放置防御塔。也就是说，对每个地⾯位置，是否放置防御塔是等概率的。 请求出这样随机放置防御塔后，期望有多少个地⾯位置能处于防御塔的攻击范围之下。 输出⼀个整数作为答案，表示答案在模 的意义下的结果。
>
> ### 输入格式
>
> 第⼀⾏，两个正整数， ， 。 接下来 ⾏，每⾏⼀个⻓度为 的字符串。表示地图。
>
> 【提示与说明】 
>
> 【样例1解释】 随机放置⼀共有4种等概率可能： .#. ， t#. ， .#t ， t#t ，对应的攻击范围下的地⾯数量为0， 1，1，2 
>
> 因此期望为 
>
> 【数据范围】： 对30%的数据满⾜， 对60%的数据满⾜， ， 对100%的数据满⾜， 
>
> 【时间限制】：1s 【空间限制】：256MB

## 解题思路

首先我们来考虑什么情况下这个地面时不会被攻击到的：

<img src="https://cdn.luogu.com.cn/upload/image_hosting/pgt6im6p.png" style="zoom:50%;" />

每一个地面放置防御塔的概率都是$\frac{1}{2}$，那么只有当上图中所有的```.```都是没有放置的防御塔的时候，中间的地面才不会被攻击到。所以我们可以算出这个点不被攻击到的概率是$\frac{1}{2^8}$。其中分母代表的是这里相连通的点的个数的总情况数（比如说这里就有八个点）。

那么反过来说，这个点能够被攻击到的概率就是$1-\frac{1}{2^8}=\frac{2^8-1}{2^8}$。

最后，我们就只需要算出每个点的概率的和就是期望值了。

#### 另一个考点：有理数取模

这道题里面给出的模数为$1e9+7$，我们知道这个数是一个质数，那么这道题就比较好做了。

这道题中，我们只用求出逆元就可以了。我们先把分数$\frac{y}{x}$转变为$y\times x^{-1}$，那么$x^{-1}$就是$x$的逆元，那我们知道在模数为质数的情况下$x^{-1}\equiv x^{p-2}(mod\ p)$（费马小定理）。这样我们就可以算出有理数取模了（$\frac{y}{x} \equiv y\times x^{p-2}$）

总体思路就是这样了，下面是一些优化和具体实现。

### 优化

找到每一个点对应的行和列里面有多少个没有被挡住的地面有两种方法，第一种就是非常暴力的枚举每一个点，并求出对应的地面数量。这种做法可以过掉所有数据点（因为没有$hack$）。然而，数据经过同学们的不屑努力下，发现当所有的点都是地面的时候，这种算法会完全的炸掉。那么我们就想办法优化他，我们没有必要每次都从枚举的当前点像四个方向找到所有的地面数量，我们只用在原图中找到所有的连续地列和行（一个点也算列和行），这样我们只需要$O(2nm)$的复杂度就可以求出每一个点对应的地面总数（但是对于墙壁很多的数据点，会比朴素算法更慢，但是完全能过）。

然而发现优化后在老年评测机上还是会$TLE$，我们继续优化，我们将$2^1$到$2^{n\times m}$的值预处理出来，不用每次都用快速幂算，这样成功的过掉了这道题

## 最后放上代码

```cpp
#include<bits/stdc++.h>
using namespace std;
const int mod=1e9+7;
int read(){
	int x=0,f=1;
	char c=getchar();
	while(c<'0'||c>'9'){
		if(c=='-') f=-1;
		c=getchar();
	}
	while(c>='0'&&c<='9'){
		x=x*10+c-'0';
		c=getchar();
	}
	return x*f;
}
void write(int x){
	if(x<0) putchar('-'),x=-x;
	if(x>9) write(x/10);
	putchar(x%10+'0');
	return ;
}
int qpow(int x,int up){
	int result=1;
	for(;up;up>>=1){
		if(up&1) result=(long long)result*x%mod;
		x=(long long)x*x%mod;
	}
	return result;
}
struct point{
	int h;
	int l;
}a[2005][2005];
int n,m,ans=0,pow2[4005];
char mmap[2005][2005],c;
int main(){
	freopen("cover.in","r",stdin);
	freopen("cover.out","w",stdout);
	for(int i=1;i<=4000;i++){
		pow2[i]=qpow(2,i);
	}
	n=read();
	m=read();	
	for(int i=1;i<=n;i++){
		c=getchar();
		while(c!='#'&&c!='.') c=getchar();
		mmap[i][1]=c;
		for(int j=2;j<=m;j++){
			mmap[i][j]=getchar();
		}
	}
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			if(a[i][j].h==0){
				bool flag1=0;
				int pos1=m;
				for(int k=j;k<=m;k++){
					if(mmap[i][k]=='#'){
						flag1=1;
						pos1=k;
						break;
					}
				}
				if(flag1==1){
					a[i][j].h=pos1-j;
				}
				else{
					a[i][j].h=pos1-j+1;
				}
				if(flag1==1){
					for(int k=j;k<pos1;k++){
						a[i][k].h=a[i][j].h;
					}
				}
				else{
					for(int k=j;k<=pos1;k++){
						a[i][k].h=a[i][j].h;
					}
				}
			}
			if(a[i][j].l==0){
				bool flag2=0;
				int pos2=n;
				for(int k=i;k<=n;k++){
					if(mmap[k][j]=='#'){
						flag2=1;
						pos2=k;
						break;
					}
				}
				if(flag2==1){
					a[i][j].l=pos2-i;
				}
				else{
					a[i][j].l=pos2-i+1;
				}
				if(flag2==1){
					for(int k=i;k<pos2;k++){
						a[k][j].l=a[i][j].l;
					}
				}
				else{
					for(int k=i;k<=pos2;k++){
						a[k][j].l=a[i][j].l;
					}
				}
			}
			int tt=a[i][j].h+a[i][j].l-1;
			ans=(ans+(long long)(pow2[tt]-1)*qpow(pow2[tt],mod-2)%mod)%mod;
		}
	}
	write(ans);
	return 0;
}
```

