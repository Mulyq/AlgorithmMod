[Codeforces Round #789 (Div. 2)](https://codeforces.com/contest/1678)

##### A. Tokitsukaze and All Zero Sequence

题目大意：

给定长度为$n$的数组，可以进行两种操作：1、如果$a_i = a_j$，则使其中一个等于$0$；2、使$a_i=a_j=min(a_i,a_j)$。问用最少的步数使数组均为$0$。

解题思路：

如果数组中存在$0$，则使其余非$0$元素与$0$进行操作2；如果数组中存在相同元素，则先对这两个相同元素进行操作1，再重复第一种情况；对于其余情况，先进行一次操作2，再视为第二种情况处理。

参考代码：

```cpp
void solve() {
    vector<int> a(110);
    int n;
    cin >> n;
    for(int i = 0; i < n; i ++) {
        int x;
        cin >> x;
        a[x] ++;
    }
    int res = n + 1;
    for(int i = 0; i <= 100; i ++) {
        if(i == 0 && a[i] > 0) {
            res = n - a[i];
            break;
        }
        if(a[i] >= 2) {
            res --;
            break;
        }
    }
    cout << res << '\n';
}
```



##### B1. Tokitsukaze and Good 01-String (easy version)

题目大意：

给定一个01字符串，每次操作改变一个字符，使得用最少的步数相同字符串的长度为偶数。

解题思路：

1、统计当前记录串的$0$或者$1$的个数，若个数为奇数，且与下个位置不同，则修改下个位置的字符。

2、将字符下标成对记录，即{0,1}，{2,3}……若出现$01$或者$10$则表示要修改为$00$或者$11$，需要修改的次数$+1$。

参考代码：

1、

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    for(int i = 0; i < n; i ++) {
        char x;
        cin >> x;
        a[i] = x - '0';
    }
    int res = 0;
    int cnt = 1;
    for(int i = 1; i < n; i ++) {
        if(a[i] != a[i - 1] ) {
            if(cnt % 2 == 1) {
                a[i] = a[i - 1];
                res ++;
                cnt ++;
            } else {
                cnt = 1;
            }
        } else {
            cnt ++;
        }
    }
    cout << res << '\n';
}
```

2、

```cpp
void solve() {
    int n;
    cin >> n;
    vector<PII> a(n / 2);
    for(int i = 0; i < n / 2; i ++) {
        char x, y;
        cin >> x >> y;
        a[i].first = x - '0', a[i].second = y - '0';
    }
    int res = 0;
    for(int i = 0; i < n / 2; i ++) {
        if(a[i].second != a[i].first) {
            res ++;
        }
    }
    cout << res << '\n';
}
```



##### B2. Tokitsukaze and Good 01-String (hard version)

题目大意：

在B1的基础上增加了要求最小分成$0$串和$1$串的个数和。

解题思路：

延续B1第二种的思路，由于$10$和$01$可以任意的修改成为$00$或者$11$，但是原本的$00$或者$11$是无法修改的，这样会增加最小修改次数，因此出现$xx00xxxx11xx$的情况一定会被分割成两部分。同时考虑全为$10$或者$01$的情况。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<PII> a(n / 2);
    for(int i = 0; i < n / 2; i ++) {
        char x, y;
        cin >> x >> y;
        a[i].first = x - '0', a[i].second = y - '0';
    }
    int res1 = 0, res2 = 0;
    int o = -1;
    for(int i = 0; i < n / 2; i ++) {
        if(a[i].second != a[i].first) {
            res1 ++;
        } else {
            if(o != a[i].first) {
                res2 ++;
                o = a[i].first;
            }
        }
    }
    res2 = max(res2, 1);
    cout << res1 << ' ' << res2 << '\n';
}
```



##### C. Tokitsukaze and Strange Inequality

题目大意：

给定一种排列$p$，要求找出四元组的个数。四元组$[a,b,c,d]$满足$p_a<p_c$平且$p_b> p_d$。

解题思路：

由于数据规模，可以暴力选取$b$和$c$，在$[c+1,n]$中选择大于$p_b$的个数，在$[1,b - 1]$中选择小于$p_c$的个数，二者相乘。个数可以由$O(n^2)$的前缀和预处理得到。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n + 1);
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    vector<vector<int>> cnt1(n + 1, vector<int> (n + 1)), cnt2(n + 1, vector<int> (n + 1));
    for(int j = 1; j <= n; j ++) {
        for(int i = 1; i <= n; i ++) {
            if(a[i] < j) {
                cnt1[i][j] ++;
            }
        }
    }
    for(int j = 1; j <= n; j ++) {
        for(int i = 1; i <= n; i ++) {
            cnt1[i][j] += cnt1[i - 1][j];
        }
    }

    for(int j = 1; j <= n; j ++) {
        for(int i = n; i >= 1; i --) {
            if(a[i] < j) {
                cnt2[i][j] ++;
            }
        }
    }
    for(int j = 1; j <= n; j ++) {
        for(int i = n - 1; i >= 1; i --) {
            cnt2[i][j] += cnt2[i + 1][j];
        }
    }
    ll res = 0;
    for(int i = 1; i < n; i ++) {
        for(int j = i + 1; j < n; j ++) {
            int x = cnt1[i - 1][a[j]];
            int y = cnt2[j + 1][a[i]];
            res += x * y;
        }
    }
    cout << res << '\n';
}
```



##### D. Tokitsukaze and Meeting

题目大意：

有$n$行$m$列的会场，有$n*m$ 个同学，$0$表示顽皮的同学，$1$表示认真的同学。同学们按照图示的顺利进入会场，问每一个同学进入会场时有多少行至少有一个认真的同学，有多少列至少有一个认真的同学，答案取二者之和。

![img](C:\Users\Moniq\Desktop\ACM\all.assets\6adf53c6760706e7188b39c11a0473a15cb0ae60.png)

解题思路：

发现每个同学的相对列数差是一个定值，我们取会场坐满时每位同学的列号作为对答案的价值。同时对于行而言，第$i$的同学入场时的价值等于第$i - m$个同学入场时的价值加上$[i - m + 1, i]$个同学中有无认真的同学，可以通过记录上一个同学的编号来简单判断。

参考代码：

```cpp
void solve() {
    int n, m;
    string s;
    cin >> n >> m >> s;
    vector<int> cres(n * m), rres(n * m);
    vector<int> row(n), col(m);
    for(int i = 0; i < s.size(); i ++) {
        if(s[i] == '1') {
            if(col[i % m] == 0) {
                col[i % m] = 1;
                cres[i] ++;
            }
        }
    }
    for(int i = 1; i < n * m; i ++ ) {
        cres[i] += cres[i - 1];
    }
    int l = -INF;
    for(int i = 0; i < s.size(); i ++) {
        if(i - m >= 0) {
            rres[i] = rres[i - m];
        }
        if(s[i] == '1') {
            l = i;
        }
        if(i - l < m) {
            rres[i] ++;
        }
    }
    for(int i = 0; i < n * m; i ++) {
        cout << rres[i] + cres[i]  << ' ';
    }
    cout << '\n';
}
```



##### E. Tokitsukaze and Two Colorful Tapes

题目大意：

给定两条长度为$n$的彩带，其中一条颜色排列为$a$，另一条为$b$，可以规定每种颜色的分数$c(1 <= c <= n)$，每个位置对答案的价值为$|c[a[i]] - c[b[i]]|$，求$\sum_{i=1}^{n}|c[a[i]] - c[b[i]]|$的最大值。

解题思路：

首先找出所有环，显然对于长度为偶数的环，以最大值，最小值，最大值的排列顺序所得到的价值最大；对于长度为奇数的环，发现长度为偶数的环中得到最大值的原因是每次差值都是最大值-最小值，但是长度为奇数的的情况下然会有一个值无法发挥作用（例如：最大值-最小值-x-最大值-最小值），这样x对答案是没有贡献的。因此我们对长度为奇数的环每次在中间空一位出来，将多出来的数字填进去即可。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n + 1), b(n + 1), f(n + 1);
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    for(int i = 1; i <= n; i ++) {
        cin >> b[i];
        f[b[i]] = i;
    }
    vector<vector<int>> e;
    vector<bool> ok(n + 1);
    ll res = 0;
    for(int i = 1; i <= n; i ++) {
        int ne = f[a[i]];
        if(!ok[i]) {
            vector<int> temp;
            temp.push_back(i);
            while(a[ne] != a[i]) {
                ok[ne] = 1;
                temp.push_back(ne); 
                ne = f[a[ne]];
            }
            e.push_back(temp);
        }
        ok[i] = 1;
    }
    int l = 1, r = n;
    vector<int> soc(n + 1);
    vector<int> temp;
    for(auto x : e) {
        if(x.size() % 2 == 0) {
            for(int i = 0; i < x.size(); i += 2) {
                soc[a[x[i]]] = r --,
                soc[a[x[i + 1]]] = l ++;
            }
        } else {
            for(int i = 0; i < x.size(); i ++) {
                if(i == x.size() / 2) {
                    continue;
                }
                if(i < x.size() / 2) {
                    if(i % 2 == 0)
                        soc[a[x[i]]] = r --;
                    else 
                        soc[a[x[i]]] = l ++;
                } else {
                    if(i % 2 == 1)
                        soc[a[x[i]]] = r --;
                    else 
                        soc[a[x[i]]] = l ++;
                }
            }
            temp.push_back(x[x.size() / 2]);
        }
    }
    for(auto x : temp) {
        soc[a[x]] = l ++;
    }
    for(int i = 1; i <= n; i ++) {
        // cout << soc[a[i]] << ' ';
        res += abs(soc[a[i]] - soc[b[i]]);
    }
    cout << res << '\n';
}
```



##### F. Tokitsukaze and Permutations

题目大意：

有一个排列$p$，对其进行$k$次冒泡排序（从前往后进行）得到新的排列$a$，记录数组$a$中每个位置的价值$v_i = \sum_{j=1}^{i-1}[a_i < a_j]$。现在已知$v$数组（如果$v_i = -1$，表示此位置价值未知），问有$p$有多少种可能。

解题思路：

发现对于已知的$v$仅存在一种排列$a$与其对应。进行一次冒泡排序会使$v_i=max(v_i-1,0)$，然后$v$整体左移，$v_0$被覆盖，$v_{n - 1} = 0$。做$v_i$与$p_{(i+k)\%n}$的映射，对$i<n-k$：$p_{i+k} < k + i + 1$，$p_{i+k} <= k + 1 (v_i = 0)$，因此如果$v_i = -1$，$i$位置的贡献为$k + i + 1$；如果$v_i=0$，$i$位置的贡献为$k + 1$；如果$v_i>=1 \&\& v_i<i + 1$，$i$位置的贡献为$1$；其他情况表示不存在这种排列，贡献为$0$。对$n-k<i<n$：$p_{(i+k)\%n} < (k + i)\%n + 1$且$v_i=0||v_i=-1$，因此$i$位置的贡献为$(k + i) \% n + 1$，不满足条件则不存在这种排列，贡献为$0$。最后答案取所有贡献的乘积。

参考代码：

```cpp
void solve() {
    int n, k;
    cin >> n >> k;
    vector<int> v(n);
    for(int i = 0; i < n; i ++) {
        cin >> v[i];
    }
    ll res = 1;
    for(int i = 0; i < n; i ++) {
        if(i < n - k) {
            if(v[i] == -1) {
                res = (res * (k + i + 1)) % mod;
            } else if(v[i] == 0) {
                res = (res * (k + 1)) % mod;
            } else if(v[i] >= i + 1) {
                res = 0;
            }
        } else {
            if(v[i] == 0 || v[i] == - 1) {
                res = (res * ((k + i) % n + 1)) % mod; 
            } else {
                res = 0;
            }
        }
    }
    cout << res << '\n';
}
```



