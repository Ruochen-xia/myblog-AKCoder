# 前言

怎么又有 ABB 啊，连考三次，~~罚时吃饱了！~~

---

**Manacher**，俗称**马拉车**，是一个可以线性的求出一个串的**最长回文子串**。

如何求解**最长回文子串**呢？ ~~马拉车~~

---



#### **方法零**：

首先枚举字符串的左端点和右端点，在判断区间是否是回文串。

**时间复杂度**：$O(n^3)$

**空间复杂度**：$O(n)$

```cpp
bool check(int l,int r){
    for(int i = l,j = r;i < j;i++,j--){
        if(s[i] != s[j]) return 0;
    }
    return 1;
}
for(int d = 1;d <= n;d++){ // 这是认真的吗？
    for(int i = 1;i + d - 1 <= n;i++){
        int j = i + d - 1;
        if(check(i,j)){
            ans = max(ans,j - i + 1);
        }
    }
}
```

---

#### **方法一**：

枚举中间位置，再看看往两边扩充最大能到多少。

```cpp
for(int i = 1;i <= n;i++){
   int l,r;
   l = r = i;
   while(l >= 1 && r <= n && s[l] == s[r]) l--,r++;
   ans = max(ans,r - l + 1);
}
```





#### **方法二**：

通过~~打表~~严禁证明，可以发现，方法一是具有单调性的（长度），所以，就可以二分来搞。

> 还要用哈希来搞！



#### **方法四**：Manacher

方法三慢在扩充的过程，Manacher 就像 KMP 一样，根据已知的条件来得到最终答案。

如果回文的中心点不在上一个回文区间，那么就没有回文串与之匹配。

否则和对称的另一端和边界求一个 $\min$。（抄过来）

再暴力扩充即可！

```cpp
void init(){
  s = tmp + 1;
  for(auto v : s){
    t += '$';
    t += v;
  }
  t = '_' + t + '$';
  n_ = t.size() - 1;
  d.resize(n_ + 2);
}
for(int mid = 1,l = 1,r = -1;mid <= n_;mid++){
    int len;
    if(mid > r){
      len = 1;
    }else{
      len = min(d[l+r-mid],r-mid+1);
    }
    while(mid - len >= 1 && mid + len <= n_ && t[mid-len] == t[mid+len]){
      len++;
    }
    d[mid] = len;
    if(mid + len - 1 > r){
      r = mid + len - 1;
      l = mid - len + 1;
    }
  }
```



---

## 例题： 拉拉队排练

首先看看 $K$ 的范围：$10^{12}$，如果跟 $K$ 有关的，一定要是 $\le log_k$。

回文子串，马拉车！

在马拉车时记录，在快速幂一下就好了！

```cpp
#include <bits/stdc++.h>

using namespace std;
#define ll long long
const int N = 2e6 + 7;
const int P = 19930726;
const int inf = (1 << 30);
template<class T> void read(T &x){
  x = 0;int f = 1;char ch = getchar();
  while(!(ch >= '0' && ch <= '9')){if(ch == '-') f = -f;ch = getchar();}
  while(ch >= '0' && ch <= '9'){x = x * 10 + ch - '0';ch = getchar();}
  x *= f;
}
int n,n_,cnt[N];
ll k,ans = 1;
char tmp[N];
string s,t;
vector<int>d;
void init(){
  s = tmp + 1;
  for(auto v : s){
    t += '$';
    t += v;
  }
  t = '_' + t + '$';
  n_ = t.size() - 1;
  d.resize(n_ + 2);
}
int mcp(int x){
  return (int)ceil(x / 2.0) * 2 - 1;
}
ll ksm(ll a,ll b,ll mod){
  a %= mod;
  ll res = 1;
  while(b){
    if(b & 1){
      res *= a;
      res %= mod;
    }
    a *= a;
    a %= mod;
    b >>= 1;
  }
  return res;
}
int main(){
  read(n),read(k);
  scanf("%s",tmp+1);
  init();
  for(int mid = 1,l = 1,r = -1;mid <= n_;mid++){
    // 暴力扩充时会记录答案了！
    if(mid & 1) continue;
    int len;
    if(mid > r){
      len = 1;
    }else{
      len = min(d[l+r-mid],r-mid+1);
    }
    while(mid - len >= 1 && mid + len <= n_ && t[mid-len] == t[mid+len]){
      // cnt[mcp(len)]++;
      len++;
    }
    d[mid] = len;
    if(mid + len - 1 > r){
      r = mid + len - 1;
      l = mid - len + 1;
    }
    if(mcp(len) % 2) cnt[mcp(len)]++;
  }
  ll sum = 0;
  for(int i = n;i >= 1;i--){
    if(!(i & 1)) continue;
    sum += cnt[i];
    if(sum <= k){
      ans = (ans * ksm(i,sum,P)) % P;
      k -= sum;
    }else{
      ans = (ans * ksm(i,k,P)) % P;
      k -= sum;
      break;
    }
  }
  // for(int i = 1;i <= n;i++)
    // printf("%d %d\n",i,cnt[i]);
  if(k > 0) ans = -1;
  printf("%lld\n",ans);
  return 0;
}
```

# 后记

马拉车非常实用，一定要搞懂他！