#### FF
```cpp
const int INF = 1e9 + 7, MAXN = 2e5 + 10, mod = 998244353;
int n, m, s, t;
struct Edge {
    int to, cap, rev;
};
vector<Edge> e[MAXN];
bool vis[MAXN];
void add_e(int from, int to, int cap) {
    e[from].push_back({to, cap, (int)e[to].size()});
    e[to].push_back({from, 0, (int)e[from].size() - 1});
}

int dfs(int u, int t, int f) {
    if(u == t) return f;
    vis[u] = 1;
    for(auto &[v, c, rev] : e[u]) {
        if(!vis[v] && c > 0) {
            int d = dfs(v, t, min(f, c));
            if(d > 0) {
                c -= d;
                e[v][rev].cap += d;
                return d;
            }
        }
    }
    return 0;
}

ll max_flow(int s, int t) {
    ll flow = 0;
    while (1) {
        for(int i = 0; i <= n; i ++) {
            vis[i] = 0;
        }
        int temp = dfs(s, t, INF);
        if(temp) flow += temp;
        else return flow;
    }
    
}
```