```cpp
bool KMP(string s, string t) {
    int n = s.size(), m = t.size();
    vector<int> ne(m);
    for(int i = 1, k = 0; i < m; i ++) {
        while(k > 0 && t[i] != t[k]) {
            k = ne[k - 1];
        }
        if(t[i] == t[k]) {
            k ++;
        }
        ne[i] = k;
    }
    for(int i = 0, j = 0; i < n; i ++) {
        while(j > 0 && s[i] != t[j]) {
            j = ne[j - 1];
        }
        if(s[i] == t[j]) {
            j ++;
        }
        if(j == m) {
            return 1;
        }
    }
    return 0;
}
```