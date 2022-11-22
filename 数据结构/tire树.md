## 字典树Tire

```cpp
const int N = 1e5, M = 26;
struct Tire {
    int idx;
    vector<vector<int>> tr;
    vector<int> cnt;
    Tire() {
        idx = 0;
        tr.resize(N, vector<int> (M));
        cnt.resize(N);    
    }
    void add(string s) {
        int p = 0;
        for(int i = 0; i < s.size(); i ++) {
            int u = s[i] - 'a';
            if(!tr[p][u]) {
                tr[p][u] = ++ idx;
            }
            p = tr[p][u];
        }
        cnt[p] ++;
    }
    int query(string s) {
        int p = 0;
        for(int i = 0; i < s.size(); i ++) {
            int u = s[i] - 'a';
            if(!tr[p][u]) return 0;
            p = tr[p][u]; 
        }
        return cnt[p];
    }
};
```

##### 01tire

```cpp
const int N = 1e7;
struct Tire {
    int idx;
    int tr[N][2];
    Tire() {
        idx = 0;
        memset(tr, 0, sizeof tr);
    }
    void add(int x) {
        int p = 0;
        for(int i = 30; i >= 0; i --) {
            int u = x >> i & 1;
            if(!tr[p][u]) {
                tr[p][u] = ++idx;
            }
            p = tr[p][u];
        }
    }
    bool query(int x) {
        int p = 0;
        for(int i = 30; i >= 0; i --) {
            int u = x >> i & 1;
            if(!tr[p][u]) return 0;
            p = tr[p][u];
        }
        return 1;
    }
    int querymx(int x) {
        int p = 0, res = 0;
        for(int i = 30; i >= 0; i --) {
            int u = x >> i & 1;
            if(tr[p][!u]) {
                res += 1 << i;
                p = tr[p][!u];
            } else {
                p = tr[p][u];
            }
        }
        return res;
    }
    int querymi(int x) {
        int p = 0, res = 0;
        for(int i = 30; i >= 0; i --) {
            int u =  x >> i & 1;
            if(tr[p][u]) {
                p = tr[p][u];
            } else {
                p = tr[p][!u];
                res += 1 << i;
            }
        }
        return res;
    }
};
```