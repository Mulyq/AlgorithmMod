### Manacher

Manacher算法是一个用来查找一个字符串中的最长回文子串(不是最长回文序列)的线性算法。

```cpp
int Manacher(string s) {
    string t;
    t += "$#";
    for(char c : s) {
        t += c;
        t += '#';
    }
    int len = t.size();
    vector<int> p(len);
    int res = 0;
    for(int i = 1, r = 0, m = 0; i < len; i ++) {
        if(i <= r) p[i] = min(p[m * 2 - i], r - i + 1);
        while(t[i - p[i]] == t[i + p[i]]) p[i] ++;
        if(p[i] + i > r) r = p[i] + i - 1, m = i;
        if(p[i] > res) res = p[i];
    }
    return res - 1;
}
```