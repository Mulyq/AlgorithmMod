## 第k大的数

基于快速排序实现（建议使用BIT实现）

```cpp
int quick_sort(int q[],int l,int r,int k) {
    if(l >= r) return q[l];
    int x = q[l + r >> 1],i = l - 1,j = r + 1;
    while(i < j) {
        do i ++; while(q[i] < x);
        do j --; while(q[j] > x);
        if(i < j) swap(q[i], q[j]); 
    }
    if(j - l + 1 >= k) return quick_sort(q,l,j,k);
    else rquick_sort(q,j + 1,r,k - (j - l + 1));
}
```