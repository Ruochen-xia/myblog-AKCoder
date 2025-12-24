# T2

错因：$\log_2$ 加了 $1$

思路：

$[1,2,3,4,5]  := [1,2,0,1,2] := [0,1,0,0,1]:= [0,0,0,0,0]$

```cpp
#include <bits/stdc++.h>

using namespace std;
#define int int
#define ll long long
#define ull unsigned ll
const int MAXN = 1e5 + 7;
const int MOD = 1e9 + 7;
const int INF = 0x3f3f3f3f;
int n;
int main(){
	scanf("%d",&n);
	int cnt = 0;
	while(n > 0){
		n /= 2;
		cnt++;
	}
	printf("%d\n",cnt);
	return 0;
}

```

# T3
错因：忽略了环形

思路：设 $dp[i][j]$ 表示，传了 $i$ 次，传到第 $j$ 名同学的方案数。

```cpp
#include <bits/stdc++.h>

using namespace std;
int n,m;
int f[1001][1001];
int main()
{
	cin >> n >> m;
	f[0][1] = 1;
	for(int i = 1;i <= m;i++){
		for(int j = 1;j <= n;j++){
			int l = 0,r = 0;
			if(j == 1){
				l = n;
			}else{
				l = j - 1;
			}
			if(j == n){
				r = 1;
			}else{
				r = j + 1;
			}
			f[i][j] = f[i-1][l] + f[i-1][r];
		}
	}
	cout << f[m][1] << endl;
	return 0;
}

```

# T4
错因：写了 2 遍 dfs，但第二次不是八连通。

思路：两遍 dfs，注意第二次是八连通。

代码：

```cpp
#include <bits/stdc++.h>

using namespace std;
#define int int
#define ll long long
#define ull unsigned ll
const int MAXN = 50 + 7;
const int MOD = 1e9 + 7;
const int INF = 0x3f3f3f3f;
int t,n,m,idx;
int vis[MAXN][MAXN],vis2[MAXN][MAXN];
char s[MAXN][MAXN];
const int dx[] = {-1,0,0,1},dy[] = {0,-1,1,0};
const int ddxx[] = {-1,-1,-1,0,0,1,1,1},ddyy[] = {-1,0,1,-1,1,-1,0,1};
void dfs(int x,int y){
	vis[x][y] = idx;
	for(int i = 0;i < 4;i++){
		int xx = x + dx[i];
		int yy = y + dy[i];
		if(xx < 1 || xx > n || yy < 1 || yy > m)
			continue;
		if(!vis[xx][yy] && s[xx][yy] == '1'){
			dfs(xx,yy);
		}
	}
}

bool query(int x,int y){
	if(x < 1 || x > n || y < 1 || y > m) return 1;
	vis2[x][y] = 1;
	for(int i = 0;i < 8;i++){
		int xx = x + ddxx[i];
		int yy = y + ddyy[i];
		if(s[xx][yy] == '1' && vis[xx][yy] != idx) continue;
		if(vis2[xx][yy]) continue;
		if(query(xx,yy)) return 1;
	}
	return 0;
}

void solve(){
	scanf("%d%d",&n,&m);
	for(int i = 1;i <= n;i++) 
		for(int j = 1;j <= m;j++)
			vis[i][j] = 0;
	for(int i = 1;i <= n;i++){
		scanf("%s",s[i]+1);
	}
	int ans = 0;
	for(int i = 1;i <= n;i++){
		for(int j = 1;j <= m;j++){
			if(!vis[i][j] && s[i][j] == '1'){
				idx++;
				dfs(i,j);
				memset(vis2,0,sizeof(vis2));
				if(query(i,j)) ans++;
			}
		}
	}
	printf("%d\n",ans);
} 
int main(){
	scanf("%d",&t);
	while(t--){
		solve();
	}
	return 0;
}

```

# T5

错因：没有暴力分

思路：用树状数组维护逆序对，二分划分块数就好了

```cpp
#include <bits/stdc++.h>

using namespace std;
#define int long long
#define ll long long
#define ull unsigned ll
const int MAXN = 5e5 + 7;
const int MOD = 1e9 + 7;
const int INF = 0x3f3f3f3f;
int c[MAXN],a[MAXN],b[MAXN];
int n,m,t,idx;
int lowbit(int x){
	return x & -x;
}

void modify(int x,int val){
	while(x <= idx){
		c[x] += val;
		x += lowbit(x);
	}
}

int get_sum(int x){
	int res = 0;
	while(x > 0){
		res += c[x];
		x -= lowbit(x);
	}
	return res;
}

map<int,int>mp;
bool check(int x){
	int cnt = 1;
	int l = 1;
	int sum = 0;
	for(int i = 1;i <= n;i++){
		int pre = get_sum(idx) - get_sum(a[i]);
		if(sum + pre <= x){
			sum += pre;
		}else{
			cnt++;
			sum = 0;
			for(int j = l;j <= i-1;j++) modify(a[j],-1);
			l = i;
		}
		modify(a[i],1);
	}
	for(int j = l;j <= n;j++) modify(a[j],-1);
	return cnt <= m;
}


void solve(){
	mp.clear();
	scanf("%lld%lld",&n,&m);
	for(int i = 1;i <= n;i++){
		scanf("%lld",&a[i]);
		b[i] = a[i];
	}
	sort(b+1,b+n+1);
	idx = unique(b+1,b+n+1) - b - 1;
	for(int i = 1;i <= n;i++){
		a[i] = lower_bound(b+1,b+idx+1,a[i])-b;
	}
	int l = 0,r = n*(n-1),res; // 要从 0 开始
	while(l <= r){
		int mid = l + r >> 1;
		if(check(mid)){
			r = mid - 1;
			res = mid;
		}else{
			l = mid + 1;
		}
	}
	printf("%lld\n",res);
}
signed main(){
	scanf("%lld",&t);
	while(t--){
		solve();
	}
	return 0;
}

// map 会超时

```

