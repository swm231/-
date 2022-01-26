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
