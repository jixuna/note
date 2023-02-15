# 倍增
任意整数均可被表示成若干个2的次幂项的和，例如整数5，其二进制表示为101，该二进制数从右向左地 $0、2$ 位均为 $1$.则 $5=2^2+$ $2^0$
倍增，顾名思义就是成倍增加。若问题的状态空间特别大。则一步步地推的算法复杂度大高，可以通过倍增思想，只考察2的整数次幂位置，快速缩小求解范围，直到找到解。
# ST表
 ST算法采用了倍增思想，在 $O(nlogn)$ 的时间构造一个二维表后，可以在 $O(1)$ 的时间在线查询 $[l,r]$ 区间的最值，有效解决在线RMQ
 设 $f[i][j]$ 表示 $[i,i+2^j-1]$ 区间的最值，区间长度为 $2^j$  
 根据倍增思想，长度为$2^j-1$的区间可被分为两个长度为 $2^j-1$ 的子区间，然后求两个子区间的最值，可以写出递推公式：$f[i][j] =max(f[i][j-1],f[i+2^{j-1}][j-1] )$
## 创建ST表
若 $f[i][j]$ 表示 $[i,i+2^j-1]$ 区间的最值，区间长度为 $2^j$。若数组长度为 $n$ ，最大区间长度 $2^k \le n \le 2^{k+1}$,则 $k=[log2(n)]$
```cpp
inline void st_create(){
	int k=log2(n);
	for(int i=1;i<=n;i++){
		f[i][0]=h[i];
	}
	for(int j=1;j<=k;j++){
		for(int i=1;i<=n-(1<<j)+1;j++){
			f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
		}
	}
}
```
## ST查询
 若查询 $[l,r]$ 区间的最值，则首先计算k的值，和前面的计算方法一样，区间长度 $r-l+1$，$2^k \le r-l+1 < 2^{k+1}$，因此 $k=log2(r-l+1)$ 。若查询
区间的长度大于等于 $2^k$且小于 $2^{k+1}$，则根据倍增思想，可以将查询区间分为两个查询区间，取两个区间的最值岂可。两个区间分别为从1向后的 $2^k$ 个数及从r向前的 $2^k$个数。这两个区间可能重叠，但对求最值无影响
```cpp
inline int find(int l,int r){
	int k=log2(r-l+1);
	return max(f[l][k],f[r-(1<<k)+1][k]);
}
```
## 算法分析
创建ST时，初始化需要 $O(n)$的时间，两个for循环需要 $O(nlogn)$，总时间复杂度为 $O(nlogn)$。区间查询实际上查表的过程，计算 $k$ 的值后从表中读取两个数取最大值，因此每次查询时间为 $O(1)$。一次建表，多次使用，这种查表法就是动态规划
# RMQ
RMQ问题是区间最值查询，直接用ST表查询即可。
# 代码
```cpp
#include <iostream>
#include <cmath>
#define int unsigned long long
using namespace std;
const int N=1e6+10;
int st[N][40],a[N],n,m;
void st_create() {
	for(int i = 1; i<=n; i++)st[i][0] = a[i];
	int p = log2(n);
	for(int k = 1; k<=p; k++) {
		for(int i = 1; i<=n-(1<<k)+1; i++) {
			st[i][k] = min(st[i][k-1],st[i+(1<<(k-1))][k-1]);
		}
	}
}
int query(int l,int r) {
	int p=log2(r-l+1);
	return min(st[l][p],st[r-(1<<p)+1][p]);
}
signed main() {
	cin>>n>>m;
	for(int i=1; i<=n; i++) {
		cin>>a[i];
	}
	st_create();
	for(int i=1; i<=n-m+1; i++) {
		int l=i,r=i+m-1;
		cout<<query(l,r)<<endl;
	}
	return 0;
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE4MzE3MDM5ODUsMjAzMDg0NTAyOSwtMT
A5Nzc1NjkxNywtMTk2OTIzMzk4MV19
-->