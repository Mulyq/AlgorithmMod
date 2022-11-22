Codeforces Round #796 (Div. 2) C - F题解

连接：[Codeforces Round #796 (Div. 2)](https://codeforc.es/contest/1688)

### C. Manipulating History

题目大意：

给定一个字符串s，初始只有一个字符，每次选择s中的一个字串（不为空），替换为另一个字符串，现在给定任意排序的被替换的字串和替换的字串和最终的字符串（给定的最后一个字符串），问最初的字符是什么。

解题思路：

我们将最终的字符串视为将整个字符串替换为空，那么对于任意一个字符，我们只考虑第一次出现的位置，和最后一次消失的位置，因为中间必定会存在[消失-出现-消失-出现……]这样的偶数次。对于初始的字符来说，原本就有，就不存在出现的位置，但存在消失的位置；对于其他字符来说，存在出现的位置和消失的位置。那么初始字符出现的次数加消失的次数为奇数，其他字符为偶数，因此只要统计字符个数即可。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> cnt(26);
    for(int i = 0; i < n * 2 + 1; i ++) {
        string s;
        cin >> s;
        for(int i = 0; i < s.size(); i ++) {
            cnt[s[i] - 'a'] ++;
        }
    }
    for(int i = 0; i < 26; i ++) {
        if(cnt[i] % 2 == 1) {
            cout << char(97 + i) << '\n';
        }
    }
}
```

### D. The Enchanted Forest

解题思路：

如果我们重新走过之前走过的路就会相当于这个点是从-t开始长起的，这显然比任意一个a都要小，因此一个优秀的猎人是不会走回头路的。但是如果k>n呢？从头走到尾在返回吗？不妨这么想，在有限的时间内，生成的价值总量是相同的，那么得到的价值就是总的价值减去剩下的价值，那么这个问题就变成了如何走可以使得剩下的价值最小。欸，我们之前考虑的时候认为走过之前走过的路就相当于从-t开始，那么只要让$\sum_{i=1}^{n}-t_i$最小就可以了，那么$t_i$相差的最小值为1，那么只要构成一个等差数列就可以了，即先在起点处等待，可以恰好走完一趟时出发，因此一个优秀的猎人会有耐心等待，待时机成熟时再出发。

参考代码：

```cpp
void solve() {
    int n, k;
    cin >> n >> k;
    vector<ll> a(n + 1);
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    vector<ll> b(n + 1);
    for(int i = 1; i <= n; i ++) {
        b[i] = a[i] + b[i - 1];
    }
    ll res = 0;
    if(k <= n) {
        for(int i = 1; i <= n; i ++) {
            if(i + k - 1 > n) break;
            res = max(res, b[i + k - 1] - b[i - 1]);
        }
        cout << res + k * (k - 1) / 2 << '\n';
    } else {
        res = b[n] - b[0] + (k - n + k - 1) * n / 2;
        cout << res << '\n'; 
    }
}
```

### E. Railway System

解题思路：

Kruskal就可以了。

具体讲一下的话就是先问每条边的长度，排序，然后尝试让每条边都加入到集合，如果得到的结果与不加入这个边的结果之差为这条边的长度，那么就加入这条边到集合中。

参考代码：

```cpp
int quer(string s) {
    cout << "? " << s << '\n';
    int x;
    cin >> x;
    return x;
}
void solve() {
    int n, m;
    cin >> n >> m;
    vector<PII> e;
    for(int i = 0; i < m; i ++) {
        string s;
        for(int j = 0; j < m; j ++) {
            if(i == j) {
                s += '1';
            } else {
                s += '0';
            }
        }
        int val = quer(s);
        e.push_back({val, i});
    }
    sort(e.begin(), e.end());
    int res = 0;
    string s;
    for(int i = 0; i < m; i ++) {
        s += '0';
    }
    for(int i = 0; i < m; i ++) {
        s[e[i].second] = '1';
        int temp = quer(s);
        if(temp - res == e[i].first) {
            res = temp;
        } else {
            s[e[i].second] = '0';
        }
    }
    cout << "! " << res << '\n';
}
```



### F. Sanae and Giant Robot

解题思路：

首先对一段区间进行操作后，a[i] = b[i] ，这对与其他区间进行操作时，这些点的贡献为0。设置一个集合为未操作的点，并且考虑依次处理他们。

参考代码：

```cpp
void solve() {
    int n, m;
    cin >> n >> m;
    vector<ll> a(n + 1);
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    for(int i = 1; i <= n; i ++) {
        int x;
        cin >> x;
        a[i] -= x;
    }
    for(int i = 1; i <= n; i ++) {
        a[i] += a[i - 1];
    }
    set<int> S;
    queue<int> q;
    vector<vector<int>> e(n + 1);
    for(int i = 0; i <= n; i ++) {
        if(a[i]) S.insert(i);
        else q.push(i);
    }
    for(int i = 0; i < m; i ++) {
        int l, r;
        cin >> l >> r;
        e[l - 1].push_back(r);
        e[r].push_back(l - 1);
    }
    while(q.size()) {
        int p = q.front();
        q.pop();
        for(auto v : e[p]) {
            if(S.find(v) == S.end()) {
                if(v < p) swap(v, p);
                auto i = S.lower_bound(p), j = S.upper_bound(v);
                while(i != S.end() && i != j) {
                    q.push(*i);
                    S.erase(i ++);
                }
            }
        }
    }
    if(S.size()) cout << "NO\n";
    else cout << "YES\n";
}
```

