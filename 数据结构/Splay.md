```cpp
int root, tot;
const int MAXN = 1e5 + 10;
struct ND {
    int ch[2],  // 儿子
        f,      // 父亲
        siz,    // 子树大小
        val,    // 值
        cnt;    // 个数
} nd[MAXN];

void update(int x) {
    nd[x].siz = nd[nd[x].ch[0]].siz + nd[nd[x].ch[1]].siz + nd[x].cnt;
}

void rotate(int x) {
    int y = nd[x].f;
    int z = nd[y].f;
    int k = nd[y].ch[1] == x;
    nd[z].ch[nd[z].ch[1] == y] = x;
    nd[x].f = z;
    nd[y].ch[k] = nd[x].ch[k ^ 1];
    nd[nd[x].ch[k ^ 1]].f = y;
    nd[x].ch[k ^ 1] = y;
    nd[y].f = x;
    update(y), update(x);
}

void splay(int x, int goal) {
    while(nd[x].f != goal) {
        int y = nd[x].f;
        int z = nd[y].f;
        if(z != goal) {
            if ((nd[z].ch[1] == y) == (nd[y].ch[1] == x)) {
                rotate(y);
            } 
        }
        rotate(x);
    }
    if(goal == 0) {
        root = x;
    }
}

void find(int x) {
    int u = root;
    if(!u) {
        return;
    }
    while(nd[u].ch[x > nd[u].val] && x != nd[u].val) {
        u = nd[u].ch[x > nd[u].val];
    }
    splay(u, 0);
}

void insert(int x) {
    int u = root, f = 0;
    while(u && nd[u].val != x) {
        f = u;
        u = nd[u].ch[x > nd[u].val];
    }
    if(u) {
        nd[u].cnt ++;
    } else {
        u = ++ tot;
        if(f) {
            nd[f].ch[x > nd[f].val] = u;
        }
        nd[u].ch[0] = nd[u].ch[1] = 0;
        nd[u].f = f;
        nd[u].val = x;
        nd[u].cnt = 1;
        nd[u].siz = 1;
    }
    splay(u, 0);
}

int Next(int x) {
    find(x);
    int u = root;
    if(nd[u].val > x) return u;
    u = nd[u].ch[1];
    while(nd[u].ch[0]) u = nd[u].ch[0];
    return u;
}

int Pre(int x) {
    find(x);
    int u = root;
    if(nd[u].val < x) return u;
    u = nd[u].ch[0];
    while(nd[u].ch[1]) u = nd[u].ch[1];
    return u;
}

void del(int x) {
    int pre = Pre(x), next = Next(x);
    splay(pre, 0), splay(next, pre);
    int del = nd[next].ch[0];
    if(nd[del].cnt > 1) {
        nd[del].cnt --;
        splay(del, 0);
    } else {
        nd[next].ch[0] = 0;
    }
}

int kth(int k) {
    int u = root;
    if(nd[u].siz < k) {
        return 0;
    }
    while(1) {
        int y = nd[u].ch[0];
        if(k > nd[y].siz + nd[u].cnt) {
            k -= nd[y].siz + nd[u].cnt;
            u = nd[u].ch[1];
        } else {
            if(k <= nd[y].siz) {
                u = y;
            } else {
                return nd[u].val;
            }
        }
    }
}

#include <bits/stdc++.h>
using namespace std;
int main() {
    ios::sync_with_stdio(0), cin.tie(0);
    int q;
    cin >> q;
    insert(1e9), insert(-1e9);
    while(q --) {
        int op, x;
        cin >> op >> x;
        switch (op) {
            case 1:
                insert(x);
                break;
            case 2:
                del(x);
                break;
            case 3:
                find(x);
                cout << nd[nd[root].ch[0]].siz << '\n';
                break;
            case 4:
                cout << kth(x + 1) << '\n';
                break;
            case 5:
                cout << nd[Pre(x)].val << '\n';
                break;
            case 6:
                cout << nd[Next(x)].val << '\n';
                break;
        }
    }
}
```