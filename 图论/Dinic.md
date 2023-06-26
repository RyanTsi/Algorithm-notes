```cpp
int n, m, s, t;
struct Edge {
    int to, cap, rev;
};
vector<Edge> e[MAXN];
int iter[MAXN];
int level[MAXN];
void add_e(int from, int to, int cap) {
    e[from].push_back({to, cap, (int)e[to].size()});
    e[to].push_back({from, 0, (int)e[from].size() - 1});
}
int dfs(int u, int t, int f) {
    if(u == t) return f;
    for(int &i = iter[u]; i < e[u].size(); i ++) {
        auto &[v, c, rev] = e[u][i];
        if(level[u] < level[v] && c > 0) {
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
void bfs() {
    for(int i = 0; i <= n; i ++) {
        level[i] = -1;
    }
    queue<int> q;
    level[s] = 0;
    q.push(s);
    while(q.size()) {
        int u = q.front();
        q.pop();
        for(auto &[v, c, rev] : e[u]) {
            if(level[v] == -1 && c > 0) {
                level[v] = level[u] + 1;
                q.push(v);
            }
        }
    }
}
ll max_flow(int s, int t) {
    ll flow = 0;
    while (1) {
        bfs();
        if(level[t] < 0) return flow;
        for(int i = 0; i <= n; i ++) {
            iter[i] = 0;
        }
        int f;
        while((f = dfs(s, t, INF)) > 0) {
            flow += f;
        }
    }
    
}
```