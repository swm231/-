自动ac机，顾名思义，就是可以自动帮你ac的机器，学会它，将不再用于被wa,tle,ce等等支配的恐惧，你...值得拥有！！  

ok,那我们开始吧  
ac自动机是对模式串进行匹配的过程  
首先我们需要对模式串建trie树，这个模式串可以是字母串，也可以是数字串（我猜的）  
  
下面是建立tire树的一般代码  
int tr[N][26], idx = 0;    //N是所有模式串中字符的综合，因为每一个字符都需要一个idx值来储存其信息。当模式串前缀没有被建立时，我们需要新的idx值来储存当前字符的信息  
```cpp
void insert(string s)
{
    int p = 0;
    for(int i = 0; s[i]; i++)
    {
        int t = s[i] - 'a';
        if(!tr[p][t]) tr[p][t] = ++idx;
        p = tr[p][t];
    }
}
```
在匹配某一模式串的过程中，如果匹配成功，也就是匹配到了模式串的最后一个字符，那么我们需要对这里进行标记，以便于我们以后的操作（标记这个东东下面再说）；如果匹配失败，我们需要利用在这一模式串匹配过程中的信息来跳到下一模式串的匹配中，所以这里需要一个fail的指针，也就是说，它指向了在匹配模式串过程中失败时我们跳到下一模式串的某个位置。  
下面是建立fail指针的过程  
在这再提一嘴，建立fail指针的过程是bfs的过程，需要对树上26个子节点全部更新，我们使用队列实现  
```cpp
int fail[N];
queue<int> q;
void build()
{
	  for (int i = 0; i < 26; i++)
		    if (tr[0][i])
			      q.push(tr[0][i]);
	  while (q.size())
	  {
		    int f = q.front(); q.pop();  //f是父亲节点
		    for (int i = 0; i < 26; i++)
		    {
			      int p = tr[f][i];  //p就是我们当前fail指针出发的节点
			      if (!p) tr[f][i] = tr[fail[f]][i];   //当这个节点不存在时，我们把它更新一下
			      else
			      {
				        fail[p] = tr[fail[f]][i];    //当这个节点存在时，我们把它指向其树上父亲fail指向的节点对应的子节点
				        q.push(p);
			      }
		    }
	  }
}
```
到此，我们我ac自动机的初始化工作就完成啦，具体有什么用呢，我也不知道🤷‍♂️  
当然，各位巨佬看到这里肯定是一点压力也没有，我再说一下今天刚刚学的对于fail树的建立吧，  
那它又有什么用呢，答案是我还是不知道  

fail树其实就是把我们建立的fail指针反过来，原来应该是很多个节点指向一个节点，而我们建立的fail是一个节点指向很多个节点  
当我们来寻找fail树上的信息时，可以使用bfs来寻找  
下面是建立fail树的过程：  
```cpp
int head[N], to[N], nxt[N], cnt = 0;  //head储存的是i的一个子节点的idx值；to储存第idx个fail指针的起点，反过来之后就是终点；nxt指向第idx个fail指针指向的上一个fail指针（一直到0）
                                      //因为我们在反向建树的时候是遍历的idx，对树上边的标记也是从1开始的，所以这个to指针就是顺序存下来的
viod build_failtree()
{
    for(int i = 1; i <= idx; i++)
    {
        int u = fail[i], v = i;
        nxt[+cnt] = head[u];
        head[u] = cnt;
        to[cnt] = v;
    }
}
```
这样，fail树就建立好了，我们可以用bfs来遍历树上的每个节点  
差不多也就这样了，我们来写一道模板题吧  
<a href="https://www.luogu.com.cn/problem/P5357" target="_blank">szgsb</a>

```cpp
#include<iostream>
#include<queue>
using namespace std;
const int N = 2e5 + 10;
int tr[N][26], fail[N], n, idx = 0, pos[N];
int sz[N], head[N], nxt[N], to[N], cnt = 0;
queue<int> q;
char s[N * 10];
void insert(int x)
{
	int p = 0;
	for (int i = 0; s[i] != '\0'; i++)
	{
		int t = s[i] - 'a';
		if (!tr[p][t]) tr[p][t] = ++idx;
		p = tr[p][t];
	}
	pos[x] = p;
}
void build()
{
	for (int i = 0; i < 26; i++)
		if (tr[0][i])
			q.push(tr[0][i]);
	while (q.size())
	{
		int f = q.front(); q.pop();
		for (int i = 0; i < 26; i++)
		{
			int p = tr[f][i];
			if (!p) tr[f][i] = tr[fail[f]][i];
			else
			{
				fail[p] = tr[fail[f]][i];
				q.push(p);
			}
		}
	}
}
void build_fail(int u, int v)
{
	nxt[++cnt] = head[u];
	head[u] = cnt;
	to[cnt] = v;
}
void bfs(int u)
{
	for (int i = head[u]; i; i = nxt[i])
	{
		int v = to[i];
		bfs(v);
		sz[u] += sz[v];
	}
}
int main()
{
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
	{
		scanf("%s", s);
		insert(i);
	}
	build();
	scanf("%s", s);
	int p = 0;
	for (int i = 0; s[i] != '\0'; i++)
	{
		int t = s[i] - 'a';
		p = tr[p][t];
		sz[p]++;
	}
	for (int i = 1; i <= idx; i++)
		build_fail(fail[i], i);
	bfs(0);
	for (int i = 1; i <= n; i++)
	{
		printf("%d\n", sz[pos[i]]);
	}
}
```
sz函数记录了每个节点出现的次数，但是它只统计了tire树从树根到这个节点这一段n个字符的出现次数，并没有统计其后缀n-1,n-2...1这些字符的次数。  
通过模拟图发现，fail树中的指针是当当前模式串的前缀等于另一模式串的子字符串时，才让前缀的最后一个字符指向了与其相等的另一模式串的子字符串（这两个模式串可以是同一个串）。  
在统计各个模式串出现的次数时，它都有可能是某一模式串的子串，当父串出现时，那么其中的子串都要统计。这个再建立完fail树后通过深搜实现，从下往上统计所有字符串出现的次数（从最长的字符到一个字符），然后通过标记每一个模式串的pos数组可以求出各个模式串出现的次数了。  
  
任重而道远  
