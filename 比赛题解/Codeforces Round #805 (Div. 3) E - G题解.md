[Codeforces Round #805 (Div. 3)](https://codeforces.com/contest/1702)

## E. Split Into Two Sets

首先考虑每种数出现的次数必须为$2$，因为有$2*n$个数，每个数不大于$n$。

并且每张牌的两个数不能相等。

其次考虑如何将牌分成两堆。下面给出两种思路：

一、由于每张牌对应两个数字，每个数字对应两张牌，那么每个数字对应的两种牌就不能被分到同一边。可以考虑在每个数字对应的两张牌之间建一条边，判读其是否为二分图。

二、考虑一个环，例如：

|1| |2| |3| |4|
|2| |3| |4| |1|

我们发现当环长度为偶数时，每间隔一张牌分为一堆是可行的。但是当环为奇数时就不能满足条件，因此，可以判断图中的每个环是否都为偶数来。

参考代码：

一、

```cpp
const int MAXN = 2e5 + 10;
vector<int> e[MAXN];
int color[1000010];
int n;
bool dfs(int v, int c) {
    color[v] = c;
    for(auto u : e[v]) {
        if(color[u] == c) {
            return 0;
        } else if(color[u] == 0 && !dfs(u, -c)) {
            return 0;
        }
    }
    return 1;
}
bool check() {
    for(int i = 0; i < n; i ++) {
       	if(color[i] == 0) {
        	if(!dfs(i, 1)) {
               	return 0;
           	}
       	}
   	}
   	return 1;
}
void solve() {
    cin >> n;
    vector<vector<int>> tt (n);
    bool ok = 1;
    for(int i = 0; i < n; i ++) {
        int u, v;
        cin >> u >> v;
        u --, v --;
        tt[u].push_back(i);
        tt[v].push_back(i);
        if(u == v) ok = 0;
    }
    for(int i = 0; i < n; i ++) {
        if(tt[i].size() != 2) ok = 0; 
    }
    if(!ok) {
        cout << "NO\n";
        return ;
    }
    for(int i = 0; i < n; i ++) {
        int u = tt[i][0], v = tt[i][1];
        e[u].push_back(v);
        e[v].push_back(u);
    }
    if(check()) {
        cout << "YES\n";
    } else {
        cout << "NO\n";
    }
    for(int i = 0; i < n; i ++) {
        e[i].clear();
        color[i] = 0;
    }
}
```

二、

```cpp
const int MAXN = 2e5 + 10;
vector<int> e[MAXN];
vector<int> vis(MAXN);
int n;
int dfs(int r, int p, int len) {
    vis[r] = 1;
    for(auto v : e[r]) if(v != p && !vis[v]) {
        return dfs(v, r, len + 1);
    }
    return len;
}
void solve() {
    cin >> n;
    for(int i = 0; i < n; i ++) {
        e[i].clear();
        vis[i] = 0;
    }
    bool ok = 1;
    for(int i = 0; i < n; i ++) {
        int a, b;
        cin >> a >> b;
        if(a == b) ok = 0;
        a --, b --;
        e[a].push_back(b), e[b].push_back(a);
    }
    for(int i = 0; i < n; i ++) {
        if(e[i].size() != 2) {
            ok = 0;
            break;
        }
    }
    if(!ok) {
        cout << "NO\n";
        return ;
    }
    for(int i = 0; i < n; i ++) {
        if(!vis[i]) {
            int len = dfs(i, - 1, 0);
            if(len % 2 == 0) {
                cout << "NO\n";
                return ;
            }
        }
    }
    cout << "YES\n";
}
```

## F. Equate Multisets

首先一个大的偶数可以由一个小的奇数$x$和$2^k$的乘积得到，因此我可以将a和b中的元素进行操作直到全为奇数。

这样问题就转化为了$b$中的元素能否通过整除$2$转化为$a$。

做法：建立一个优先队列存储b中元素，每次拿出b中最大的元素x，判断x是否在a中，如果不在，则将x/2压入优先队列。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    multiset<int> a;
    priority_queue<int> b;
    for(int i = 0; i < n; i ++) {
        int x;
        cin >> x;
        while(!(x & 1)) {
            x >>= 1;
        }
        a.insert(x);
    }
    for(int i = 0; i < n; i ++) {
        int x;
        cin >> x;
        while(!(x & 1)) {
            x >>= 1;
        }
        b.push(x);
    }
    while(a.size()) {
        int x = b.top();
        b.pop();
        auto it = a.find(x);
        if(it != a.end()) {
            a.erase(it);
        } else {
            x >>= 1;
            b.push(x);
        }
        if(!x) {
            cout << "NO\n";
            return ;
        }
    }
    cout << "YES\n";
}
```

## G. Passable Paths

定义dis(U,v)为树上两点u，v的距离。

对于每次询问的序列，可以定义链的左端点lp和右端点rp。对于新加入的点u，如果dis(lp, rp) = dis(lp, u) + dis(u, rp),则说明u在链中间，如果dis(lp, u) = dis(lp, rp) + dis(rp, u)，则说明可以更新新的链为(lp，u), 如果dis(u, rp) = dis(u, lp) + dis(lp, rp)，则说明可以更新新的链为(u, rp),其他情况则说明链不存在。

参考代码：
```cpp
const int MAXN = 2e5 + 10;
int dep[MAXN], fa[MAXN][20], n, m, q;
vector<int> e[MAXN];
void dfs(int u, int p) {
    dep[u] = dep[p] + 1;
    fa[u][0] = p;
    for(int v : e[u]) if(v != p) {
        dfs(v, u);
    }
}
void init() {
    dfs(1, 0);
    for(int i = 1; i <= 18; i ++) {
        for(int j = 1; j <= n; j ++) {
            fa[j][i] = fa[fa[j][i - 1]][i - 1];
        }
    }
}
int lca(int u, int v) {
    if(dep[u] < dep[v]) {
        swap(u, v);
    }
    int temp = dep[u] - dep[v];
    for(int i = 0; temp; temp >>= 1, i ++) {
        if(temp & 1) {  
            u = fa[u][i];
        }
    }
    if(u == v) return u;
    for(int i = 18; i >= 0; i --) {
        if(fa[u][i] != fa[v][i]) {
            u = fa[u][i], v = fa[v][i];
        }
    }
    return fa[u][0];
}
int dis(int u, int v) {
    return dep[u] + dep[v] - 2 * dep[lca(u, v)];
}
void solve() {
    cin >> n;
    for(int i = 1; i < n; i ++) {
        int u, v;
        cin >> u >> v;
        e[u].push_back(v);
        e[v].push_back(u);
    }
    init();
    cin >> q;
    while(q --) {
        int m;
        cin >> m;
        vector<int> p(m);
        for(int i = 0; i < m; i ++) {
            cin >> p[i];
        }
        int lp = 0, rp = 0;
        bool ok = 1;
        for(int i = 0; i < m; i ++) {
            int u = p[i];
            if(lp == 0) {
                lp = u;
            } else if(rp == 0) {
                rp = u;
            } else {
                if(dis(lp, rp) == dis(lp, u) + dis(rp, u)) {
                    continue;
                } else if(dis(lp, u) == dis(lp, rp) + dis(u, rp)) {
                    rp = u;
                } else if(dis(rp, u) == dis(lp, rp) + dis(u, lp)) {
                    lp = u;
                } else {
                    ok = 0;
                    break;
                }
            }
        }
        if(ok) {
            cout << "YES\n";
        } else {
            cout << "NO\n";
        }
    }
}
```