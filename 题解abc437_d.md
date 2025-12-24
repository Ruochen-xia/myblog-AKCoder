# 题意

给定长度分别为 $n$ 和 $m$ 的正整数序列 $a$ 和 $b$，求：
$$
\sum^n_{i=1}\sum^m_{j=1}|A_i - B_j|
$$

> 注：要对 $998244353$ 取模。

# 思路

## 思路一：

用 $O(nm)$ 的暴力，显然不能过。

```cpp
for (int i = 1; i <= n; i++) {
    for (int j = 1; j <= m; j++) {
        ans += abs(a[i] - b[j]); 
    }
}
```

## 思路二：

> 注：这里将 $A$ 排好了序。

一眼看过去，这个绝对值最讨厌。所以，对于每一个 $b_j$，都可以 $a$ 中找到一个分界线，一个是小于 $b_j$ 的，一个是大于等于 $b_j$ 的。

- 如果设分界线为 $l$，小于 $b_j$ 的贡献是：

$$
\begin{aligned}
\sum^l_{i=1}b_j - a_i &= b_j - a_1 + b_j - a_2 + \dots + b_j - a_l \\
&= b_j \times l - (a_1 + a_2 + \dots + a_l) \\
&= b_j \times l - \sum^l_{i=1} a_i
\end{aligned}
$$

可以发现 $b_j - \sum^l_{i=1} a_i$ 可以用前缀和给优化一下。

- 如果设分界线为 $r$，大于等于 $b_j$ 的贡献是：

$$
\begin{aligned}
\sum^n_{i=r}a_i - b_j &= a_r - b_j + a_{r+1} - b_j + \dots + a_n - b_j \\
&= \sum^n_{i=r} a_i - b_j \times (n - r + 1)
\end{aligned}
$$

可以发现 $\sum^n_{i=r} a_i - b_j \times (n - r + 1)$ 可以用后缀和给优化一下。

所以题目就出来了！

> 警示后人：处理的时候可能是负数，请加上模数再取模。

代码：

```cpp
void solve() {
  n = read(), m = read();
  for (int i = 1; i <= n; i++) a[i] = read();
  for (int i = 1; i <= m; i++) b[i] = read();
  sort(a + 1, a + n + 1);
  for (int i = 1; i <= n; i++) {
    sum[i] = (sum[i - 1] + a[i]) % P;
  }
  for (int i = n; i >= 1; i--) {
    suf[i] = (suf[i + 1] + a[i]) % P;
  }
  ll ans = 0;
  for (int i = 1; i <= m; i++) {
    int l = lower_bound(a + 1, a + n + 1, b[i]) - a - 1;
    int r = lower_bound(a + 1, a + n + 1, b[i]) - a;
    ll res = (((ll)b[i] * l - sum[l] + P) % P + ((suf[r] - ((ll)b[i] * (n - r + 1) + P) % P) + P) % P) % P;
    ans = (ans + res + P) % P;
  }
  printf("%lld\n", ans % P);
}
```

