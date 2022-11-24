##  Kosaraju 强连通分量分解（两次dfs)

相对于tarjan的优势，代码更短，且更容易求出连通分量间的拓扑序。
  
```cpp
const int MAXN = 1e4 + 10;
int n, m;
vector<int> e[MAXN], re[MAXN], vs;
bool vis[MAXN];
int cmp[MAXN];
void dfs(int u) {
    vis[u] = 1;
    for(auto v : e[u]) {
        if(!vis[v]) {
            dfs(v);
        }
    }
    vs.push_back(u);
}
void rdfs(int u, int k) {
    vis[u] = 1;
    cmp[u] = k;
    for(auto v : re[u]) {
        if(!vis[v]) {
            rdfs(v, k);
        }
    }
}
int scc() {
    memset(vis, 0, sizeof vis);
    vs.clear();
    for(int i = 0; i < n; i ++) {
        if(!vis[i]) dfs(i);
    }
    memset(vis, 0, sizeof vis);
    int k = 0;
    reverse(vs.begin(), vs.end());
    for(auto u : vs) {
        if(!vis[u]) {
            rdfs(u, k ++);
        }
    }
    return k;
}
```