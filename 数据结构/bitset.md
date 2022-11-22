## bitset

确实可以优化常数时间复杂度和空间复杂度的东西.

一般记作$O(\frac{n}{w})$,w=64或者32.

- 食用方法:

1. 头文件
```cpp
#include <bitset>
```

2. 指定一个容量
```cpp
bitset<1024> bs;
```

3. 运算符
懂的都懂
- `operator []`
- `operator == / !=`
- `operator &/&=/|/|=/^/^=\~`
- `operator <</>>`

4. 一些函数

- `reset()`:置0
- `set()`:置1
- `count()`:1的个数
- `size()`
- `any()`:存在1返回1
- `none()`:不存在1返回1
- `all()`:全为1返回1
- `to_string`返回字符串,
- `to_ullong`返回ull


一些需要bitset常数优化的题目:
DP优化一维 + 大力枚举
意义在于一次性实现一层DP数组的转移,当然其他是DP维护的是bool值
https://acm.hdu.edu.cn/showproblem.php?pid=7140

bitset计数 + 大力枚举
https://acm.hdu.edu.cn/showproblem.php?pid=7141