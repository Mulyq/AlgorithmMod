Codeforces Round #781 (Div. 2)
==

### A. GCD vs LCM
题目大意：给定$n$，要求输出$a$,$b$,$c$,$d$，使其满足$a + b + c + d = d$，$gcd(a, b) = lcm(c,d)$

解题思路：$\forall x (x > 0)$，$gcd(1,x) = 1$，我们可以直接输出$n - 3, 1, 1,1$，这是一个正确的构造。

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    cout << n - 3 << " 1 1 1\n";
}
```

这里我觉得有个比较值得思考的问题，如果要求输出$n$的所有解，那应该如何解决这个问题。

### B. Array Cloning Technique

题目大意：给定一个数组$a$，现在有两种操作：1、选择任意一份数组，并拷贝一份；2、选择两个元素，交换他们的位置。要求经过$k$次操作后，使得一份数组中的元素全部相同，输出$k$的最小值。

解题思路：基于倍增的想法考虑，假设元素中个数最多的元素是$p$，有$c$个，那么我们每次拷贝这个数组，就可以交换$c$次，第二次拷贝就可以交换$2c$个，依次循环（如图所示）

![](https://s3.bmp.ovh/imgs/2022/04/12/68e407abfc6dc8cb.png)

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    vector<int> a(n);
    map<int,int> mp;
    for(int i = 0; i < n; i ++) {
        cin >> a[i];
        mp[a[i]] ++;
    }
    int p = 0;
    for(auto it : mp) {
        p = max(p, it.second);
    }
    int r = n - p;
    int res = 0;
    while (r > 0) {
        res ++;
        res += min(r, p);
        r -= p;
        p *= 2;
    }
    cout << res << '\n';    
}
```

### C. Tree Infection

题目大意：给定一棵树，根节点为$1$，现在有两种感染方式：1、在已存在感染的节点的兄弟节点中选择任意一个未被感染的节点进行感染。这两种操作每秒钟进行一次，问多少时间可以感染整棵树；2、任意感染一个节点。

解题思路：发现感染不会发生在父子之间，意味着每个父节点的其中一个儿子都必须进行一次操作2。这里的做法（如图）把树转化一下变成森林（几条链），按照长度排序，假设有$w$条链，第$i$条链中有$c_i$个元素，那么经过第一种贪心操作后，每条链中元素的个数应该变成$max(0, c_i - (w - i + 1))$，然后把所有个数大于$0$的链加入到优先队列中，每进过$1$秒就将优先队列中所有的元素减$1$（操作1），并将堆顶的元素减少$1$（操作2）。但是这样时间复杂度太高了，所以引入一个tt变量，每经过$1$秒使tt加1来代替优先队列中所有元素减$1$。

![](https://s3.bmp.ovh/imgs/2022/04/12/5989ea133c219ad0.png)

参考代码：

```cpp
void solve() {
    int n;
    cin >> n;
    set<int> S;
    vector<int> cnt(n + 1);
    for(int i = 0; i < n - 1; i ++) {
        int x;
        cin >> x;
        S.insert(x);
        cnt[x] ++;
    }
    sort(cnt.begin(), cnt.end(), [&] (const int &a, const int &b) {
        return a > b;
    });
    int k = S.size() + 1;
    int temp = k;
    priority_queue<int> q;
    for(int i = 0; i < n - 1; i ++) {
        int x = cnt[i] - temp;
        temp --;
        temp = max(temp, 0);
        if(x > 0) q.push(x);
    }
    int tt = 0;
    while(q.size() && q.top() > tt) {
        int x = q.top();
        q.pop();
        x --; q.push(x);
        tt ++;
    }
    cout << k + tt << '\n';
}
```

### D. GCD Guess

题目大意：交互题，通过不超过30次询问猜出$x$的值，每次询问$a,b$得到$gcd(x + a, x + b)$

解题思路：首先知道一件事，$gcd(a,b) = gcd(a, a + b)$，这里略证：![](https://i.bmp.ovh/imgs/2022/04/12/d10d97271ea28b82.png)

那么可以依次询问二进制上的每一位那么理论上我们最方便的询问应该是$gcd(x-r,x-r+2^k)$（$r$表示$x\ mod\ 2^k$），但是这么询问是不被允许的，那么我们可以询问$gcd(x + 2^k - r,x +2^k-r + 2^{k + 1})$，这个式子的效果等价于原来是式子，相当于在我们要求的那一位二进制上加1，然后判断加完后这一位是0还是1，如果是0就表示原来这一位上是1。

参考代码：

```cpp
int ask(int a, int b) {
    cout << "?" << ' ' << a << ' ' << b << '\n';
    int x; 
    cin >> x;
    return x;
}
void solve() {
    int res = 0;
    for(int i = 0; i < 30; i ++) {
        int r = ask((1 << i) - res, (1 << i) + (1 << (i + 1)) - res);
        if(r == (1 << (i + 1))) res += (1 << i);
    }
    cout << "! " << res << '\n';
}
```

### E. MinimizOR

题目大意：给定数组$a$，长度位$n$，现有$q$次询问，每次询问$l,r$，求区间$[l,r]$最小价值，最小价值是$min(a_i|a_j)\{l <= i,j <= r,i \neq j\}$

解题思路：$a_i < 2^{30}$，可证明最小或和只会在最小的$31$个数字中产生。官方题解中是用数学归纳法证明的，我的想法是可能可以用鸽笼原理证明。

参考代码：

```cpp
const int MAXN = 1e5 + 10;
int a[MAXN];
struct node {
    vector<int> num;
} d[MAXN << 2];
void build(int k, int l, int r) {
    if(l == r) {
        d[k].num.push_back(a[l]);
        return ;
    }
    int m = l + r >> 1;
    build(k << 1, l , m), build(k << 1 | 1, m + 1, r);
    d[k].num.resize((int)d[k << 1].num.size() + (int)d[k << 1 | 1].num.size());
    merge(d[k << 1].num.begin(), d[k << 1].num.end(), d[k << 1 | 1].num.begin(), d[k << 1 | 1].num.end(), d[k].num.begin());
    d[k].num.resize(min(31, (int)d[k].num.size()));
}
vector<int> query(int k, int l, int r, int s, int t) {
    if(l == s && r == t) {
        return d[k].num;
    }
    int m = l + r >> 1;
    if(m >= t) {
        return query(k << 1, l, m, s, t);
    } else if(m < s) {
        return query(k << 1 | 1, m + 1, r, s, t);
    } else {
        vector<int> t1 = query(k << 1, l, m, s, m), t2 = query(k << 1 | 1, m + 1, r, m + 1, t);
        vector<int> res((int)t1.size() + (int)t2.size());
        merge(t1.begin(), t1.end(), t2.begin(), t2.end(), res.begin());
        res.resize(min(31, (int)res.size()));
        return res;
    }
    
}
void solve() {
    int n;
    cin >> n;
    for(int i = 1; i <= n; i ++) {
        cin >> a[i];
    }
    build(1, 1, n);
    int qq;
    cin >> qq;
    for(int i = 0; i < qq; i ++) {
        int l, r;
        cin >> l >> r;
        auto tt = query(1, 1, n, l , r);
        int res = 2e9;
        for(int i = 0; i < tt.size(); i ++) {
            for(int j = i + 1; j < tt.size(); j ++) {
                res = min(res, tt[i] | tt[j]);
            }
        }
        printf("%d\n", res);
    }
    for(int i = 0; i < MAXN; i ++) {
        d[i].num = vector<int> ();
    }
}
```

