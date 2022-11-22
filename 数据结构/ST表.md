## ST表

基于**倍增**的思想，利用动态规划来实现。常用来解决静态RMQ（区间最值问题）、GCD（区间最小公约数问题）等问题。预处理时间复杂度$O(nlogn)$，查询时间复杂度$O(1)$，空间复杂度$O(nlogn)$。

以RMQ问题为例我们将$[i,i + 2 ^ k - 1]$这一段区间看成两段区间取最大值 $max([i , i + 2 ^ {k - 1} - 1],[i + 2^{k - 1},i + 2 ^k - 1])$，为检查这个做法的合理里，我们需要判断这样做是否能包含所有区间。

我们对任意查询区间$[l,r]$看作$max([l,l + 2 ^ p],[r - 2 ^ p,r])$其中$p = {log_{2}{\lfloor r - l + 1\rfloor}}$就可以满足整个区间为$[l,r]$。现在我们用$f(i,j)$来表示$[i + 2^{j - 1},i + 2 ^j - 1]$这段区间，那么$max([l,l + 2 ^ p],[r - 2 ^ p,r])$就可以表示为$max(f(l, 2^p),f(r - 2 ^ p, 2^p))$。

```cpp
struct ST {
    vector<vector<int>> st;
    ST(vector<int> a) {
        int n = a.size();
        st.resize(n, vector<int>(log2(n) + 1));
        for(int i = 0; i < n; i ++) {
            st[i][0] = a[i];
        }
        for(int j = 1; j <= log2(n); j ++) {
            for(int i = 0; i + (1 << j) - 1 < n; i ++) {
                st[i][j] = max(st[i][j - 1], st[i + (1 << (j - 1))][j - 1]);
            }
        }
    }
};
```