[Educational Codeforces Round 127 (Rated for Div. 2)](https://codeforces.com/contest/1671)



### A. String Building

题目大意：给定一个字符串，问这个字符串能否由"aa","aaa","bb","bbb"构成。给定字符串仅由a和b构成

思路：发现如果存在一段连续相同的字符仅由一个字母组成，如“bbabbb”，“abb"，那么肯定就不满足条件。

参考代码：

```cpp
void solve() {
    string s = "", temp;
  	cin >> temp;
    s += '*' + temp + '*';
    int n = temp.size();
    for(int i = 1; i <= n; i ++) {
        if(s[i] != s[i - 1] && s[i] != s[i + 1]) {
            cout << "NO\n";
            return ;
        }
    }
    cout << "YES\n";
}
```



### B. Consecutive Points Segment

题目大意：给定一个数组$x$，可以对数组的每个元素操作一次，使得$x_i = x_i - 1$或者$x_i = x_i + 1$，询问是否存在可能，使得$x$组成一个等差数列，公差为$1$。给定数组$x$严格单调递增。

思路：因为有$x$严格单调递增，那么考虑$x$所包含的区间$[x_1,x_n]$，那么这个区间的长度的变化最长为$2$：$[x_1 - 1,x_n + 1]$，$[x_1 + 1,x_n - 1]$。那么只要这区间的长度在$[n - 2, n + 2]$之间都可以满足条件。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> x(n);
    for(int i = 0; i < n; i ++) {
        cin >> x[i];
    }
    if(x[n - 1] - x[0] + 1 > n + 2 || x[n - 1] - x[0] + 1 < n - 2) {
		cout << "NO\n";
    } else {
        cout << "YES\n";
    }
}
```



### C. Dolce Vita

题目大意：有$n$家商店，每家商店只每天只卖给顾客$1$包糖，且每天涨价$1$元，第$i$家商店第一天出售的价格为$a_i$。现在你每天可以收到$x$元，且这个钱第二天不能再用。问在你一包糖都买不起之前，你最多可以买多少包糖。

思路：因为第二天不能用前一天的钱，所以这是一个贪心问题。我们将$a$排序，并取前缀和，从最后一家商店开始，计算出在第几天买不起这家店的糖，最后在答案上加上前面商店的个数 * （这次算出的天数 - 上次计算的天数）。

```cpp
void solve() {
	int n, x;
    cin >> n >> x;
    vector<long long> a(n + 1), b(n + 1);
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    sort(a.begin(), a.end());
    for(int i = 1; i <= n; i ++) {
        b[i] = b[i - 1] + a[i];
    }
    long long res = 0;
    int y = 0;
    for(int i = n; i >= 1; i --) {
        if(x < b[i]) continue;
        int d = (x - b[i]) / i + 1;
        res += i * (d - y);
        y = d;
    }
    cout << res << '\n';
}
```

### D. Insert a Progression

题目大意：给定一个数组$a$和$1,2,3,...,x$，要求将$1,2,3,...,x$插入到数组$a$中，使得相邻数的绝对值之和最小。

思路：根据绝对值的性质，如果一个数列单调上升或者单调下降，那么相邻数的绝对值之和只与第一个数和最后一个数有关，插入任何一个数破坏单调性都会使得最后的和变大。那么如果数组$a$中的最大值（mx）大于等于$x$，最小值（mi）小于等于$1$，那么我们总会存在一种插入的方法使得数组$a$的相对不发生变化，即相邻数绝对值之和不变。

如果mx 小于 $x$，那么我们就要考虑将$[mx + 1, x]$这段数插入到数组$a$中，在插入的时候一定是选择将这个区间的数连续插入的，因为这样插入的增加的值只与$a[i]$,$x$,$a[i + 1]$相关，那么只要枚举插入的位置求出最小值即可，注意插到头和尾需要特殊处理。

如果mi大于$x$，与上面一种同样考虑。

参考代码：

```cpp
void solve() {
    int n, x;
    cin >> n >> x;
    vector<int> a(n);
    int mx = 0, mi = INF;
    for(int i = 0; i < n; i ++) {
        cin >> a[i];
        mx = max(mx, a[i]);
        mi = min(mi, a[i]);
    }
    ll res = 0;
    for(int i = 0; i < n - 1; i ++) {
        res += abs(a[i + 1] - a[i]);
    }
    if(x > mx) {
        int all = INF;
        all = min(all, x - a[n - 1]);
        all = min(all, x - a[0]);
        for(int i = 0; i < n - 1; i ++) {
            all = min(all, 2 * x - a[i] - a[i + 1] - abs(a[i + 1] - a[i]));
        }
        res += all;
    }
    if(1 < mi) {
        int all = INF;
        all = min(all, a[n - 1] - 1);
        all = min(all, a[0] - 1);
        for(int i = 0; i < n - 1; i ++) {
            all = min(all, a[i] + a[i + 1] - 2 - abs(a[i + 1] - a[i]));
        }
        res += all;
    }
    cout << res << '\n';
}
```

### E. Preorder

题目大意：给定一棵完全二叉树，每个节点的值只有'A'或者'B'。每个节点的前序字符串为$s[x] + f[l_x] + f[r_x]$，即该点的值加上左子树的前序字符串加上右子树的前序字符串。现在有一种操作：对换一个节点的左右子树。问根节点的前序字符串有多少种，并对$998244353$取模。

思路：树形DP。

考虑两种情况：

1、左子树的前序字符串不等于右子树的前序字符串（无论通过任意多次的对换）
说明对换会出现两种不同的答案，那么$dp[x] = (dp[2 * x] * dp[2 * x + 1] * 2) \% mod$

2、左子树的前序字符串等于右子树的前序字符串（无论通过任意多次的对换）
说明对换不影响答案，那么$dp[x] = (dp[2 * x] * dp[2 * x + 1])\%mod$

那么怎么来说明左子树的前序字符串等于右子树的前序字符串。
我们可以规定如果左子树的字典序小就先加左子树，反之先加右子树，这样我们求得的前序字符串都是字典序最小的。

参考代码：

```cpp
const int  mod = 998244353;
int n;
char s[(1 << 18) + 10];
long long dp[(1 << 18) + 10];
string dfs(int x) {
    dp[x] = 1;
    string sx = "";
    sx += s[x];
    if(2 * x  + 1 <= (1 << n) - 1) {
        string sl = dfs(2 * x), sr = dfs(2 * x + 1);
        if(sl < sr) {
            sx = sx + sl + sr;
        } else {
            sx = sx + sr + sl;
        }
        if(sl != sr) {
            dp[x] = (dp[2 * x] * dp[2 * x + 1] * 2) % mod;
        } else {
            dp[x] = (dp[2 * x] * dp[2 * x]) % mod;
        }
    }
    return sx;
}
void solve() {
    scanf("%d", &n);
    scanf("%s", s + 1);
    dfs(1);
    printf("%lld", dp[1]);
}
```



