代码中图中的点下标从0开始
  
```cpp
const int MAXN = 1e4 + 10;
int n, m;
struct Edge {
    int v;
    bool is_bri; //是否为桥（割边：删去这条边后导致整张图的联通分量增大）
};
vector<Edge> e[MAXN];
// tarjan 需要定义的
int dfn[MAXN], low[MAXN];
bool vis[MAXN];
int tim = 0;
stack<int> stk;
// 缩点需要定义的
int tag;                                        // 强连通分量个数
vector<int> tar[MAXN];                          // 每个强连通分量的点集
int belong[MAXN];                               // 每个点属于哪个强连通分量
int deg[MAXN];                                  // 缩点后新图每个点的出度
vector<int> che[MAXN];                          // 缩点后的图
// 初始化数组
void init() {
    for(int i = 0; i < tag; i ++) {
        tar[i].clear();
    }
    for(int i = 0; i < n; i ++) {
        che[i].clear();
        e[i].clear();
        dfn[i] = 0;
        low[i] = 0;
        belong[i] = 0;
        vis[i] = 0;
    }
    tim = 0;
    tag = 0;
}
void tarjan(int u, int f) {
    dfn[u] = low[u] = ++ tim;
    vis[u] = 1;
    stk.push(u);
    for(auto &[v, yes] : e[u]) {
        if(!dfn[v]) {                           // 父子边
            tarjan(v, u);
            low[u] = min(low[u], low[v]);       // 更新low
            if(dfn[u] < low[v]) {               // v不能通过u到达更早的祖宗
                yes = 1;
            }
        } else {
            if(vis[v]) {
                low[u] = min(low[u], low[v]);
            }
        }
    }
    if(low[u] == dfn[u]) {
        int now;
        do {
            now = stk.top();
            stk.pop();
            vis[now] = 0;
            tar[tag].push_back(now);
            belong[now] = tag;
        } while(now != u);
        tag ++;
    }
}
// 对所有连通分量操作
void tarjan_all() {
    for(int i = 0; i < n; i ++) {
        if(!dfn[i]) {
            tarjan(i, i);
        }
    }
}
// 缩点
void regra() {
    for(int i = 0; i < n; i ++) {
        int u = i;
        for(auto [v, yes] : e[i]) {
            int x = belong[u], y = belong[v];
            if(x != y) {
                che[x].push_back(y);
                deg[y] ++;
            }
        }
    }
}
```

- 找割点
割点：删去这个点后会导致整张图的联通分量增大
```cpp
const int MAXN = 2e5 + 10;
vector<int> e[MAXN];
int n, m, tim;
// low表示该点能到达的非父祖宗的最小时间戳
int low[MAXN], dfn[MAXN];
vector<int> res;
void tarjan(int u, int fa) {
    dfn[u] = low[u] = ++ tim;
    int c = 0;                                  // 孩子个数
    for(auto v : e[u]) {
        if(!dfn[v]) {                           // 是父子边
            tarjan(v, u);
            low[u] = min(low[u], low[v]);       // 更新low
            if(low[v] >= dfn[u] && u != fa) {   //发现必须通过父亲节点到达祖宗节点
                res.push_back(u);
            }
            c ++;
        }
        low[u] = min(low[u], dfn[v]);
    }
    // u为根节点且有大于1个之间孩子，则根节点为割点
    if(c > 1 && u == fa) {
        res.push_back(u);
    }
}
void tarjan_all() {
    for(int i = 0; i < n; i ++) {
        if(!dfn[i]) {
            tarjan(i, i);
        }
    }
}
void solve() {
    tarjan_all();
    // 可能会重复计数割点
    sort(res.begin(), res.end());
    res.resize(unique(res.begin(), res.end()) - res.begin());
}
```