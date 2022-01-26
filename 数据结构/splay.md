### 结点信息
```cpp
struct node
{
    int s[2], p, v;
    int size, lazy;
        void init(int _v, int _p)
	{
	    v = _v, p = _p;
	    size = 1;
	}
}tr[N];
```
### 固定操作
```cpp
void rotate(int x)
{
    int y = tr[x].p, z = tr[y].p;
    int k = tr[y].s[1] == x;
    tr[z].s[tr[z].s[1] == y] = x, tr[x].p = z;
    tr[y].s[k] = tr[x].s[k ^ 1], tr[tr[x].s[k ^ 1]].p = y;
    tr[x].s[k ^ 1] = y, tr[y].p = x;
    pushup(y), pushup(x);
}
void splay(int x, int k)
{
    while (tr[x].p != k)
    {
        int y = tr[x].p, z = tr[y].p;
        if (z != k)
        {
            if ((tr[y].s[1] == x) ^ (tr[z].s[1] == y)) rotate(x);
            else rotate(y);
        }
        rotate(x);
    }
    if (!k) root = x;
}
```

### 建树
```cpp
void insert(int v)
{
    int u = root, p = 0;
    while (u) p = u, u = tr[u].s[v > tr[u].v];
    u = ++idx;
    if (p) tr[p].s[v > tr[p].v] = u;
    tr[u].init(v, p);
    splay(u, 0);
}
int build(int l, int r, int p)
{
    int m = (l + r) >> 1;
    int u = nod[tt--];
    tr[u].init(w[m], p);
    if (l < m) tr[u].s[0] = build(l, m - 1, u);
    if (m < r) tr[u].s[1] = build(m + 1, r, u);
    pushup(u);
    return u;
}
```
