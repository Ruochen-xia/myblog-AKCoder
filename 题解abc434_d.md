**题意**：给定一个 $2000 \times 2000$ 的网格，当且仅当满足 $U_i \le r \le D_i$ 和 $L_i \le c \le R_i$ 时，单元格 $(r,c)$ 才会被 $i$ 覆盖。输出每一个 $k=1,2,\dots,N$，删除这一个二维区间后，求剩下有多少个点没有被覆盖。

**初步思路**：给定两个端点，要想知道点被多少个区间覆盖，可以怎么做？（二维差分，前缀和）

**思路**：可以发现，如果覆盖后，点对应区间覆盖的数量 $\le1$，就一定不会在删除后被覆盖。

当点对应区间覆盖的数量等于 $0$ 时，可以直接暴力枚举。

而点对应区间覆盖的数量等于 $1$ 时，可以使用二维前缀和来搞，这样就可以在 $O(1)$ 得出答案。

**代码**：

```cpp
void insert(int x1, int y1, int x2, int y2, int c) {
	diff[x1][y1] += c;
	diff[x2 + 1][y1] -= c;
	diff[x1][y2 + 1] -= c;
	diff[x2 + 1][y2 + 1] += c;
}
void solve() {
	read(n);
	for (int i = 1; i <= n; i++) {
		read(u[i]), read(d[i]), read(l[i]), read(r[i]);
		int x1 = u[i], y1 = l[i];	// x_1 对应的为 u ,x_2 对应的为 y;
		int x2 = d[i], y2 = r[i];	// 可以想一想 T_T
		insert(x1, y1, x2, y2, 1);	// 二维差分
	}
	for (int i = 1; i <= MAXL; i++) {
		for (int j = 1; j <= MAXL; j++) {
			diff[i][j] += diff[i - 1][j] + diff[i][j - 1] - diff[i - 1][j - 1];
			// 前缀和恢复
		}
	}
	int res = 0;
	for (int i = 1; i <= MAXL; i++) {
		for (int j = 1; j <= MAXL; j++) {
			if (!diff[i][j])  // 点对应区间覆盖的数量等于0时的数量
				res++;
		}
	}
	for (int i = 1; i <= MAXL; i++) {
		for (int j = 1; j <= MAXL; j++) {
			s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + (diff[i][j] == 1);
			// 前缀和记录
		}
	}
	for (int i = 1; i <= n; i++) {
		int x1 = u[i], y1 = l[i];
		int x2 = d[i], y2 = r[i];  // 可以想一想 T_T
		int result = s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1];
		// 前缀和
		printf("%d\n", result + res);
	}
	return;
}
```