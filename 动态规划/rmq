静态区间最值查询
时间：预处理 O(nlgn) 查询O(1)
dp[i][j]表示从i开始往后j个数字的极值
这里距离最小值
dp[i][j] = min(dp [i][j - 1], dp [i + (1 << j - 1)][j - 1]);

初始化
void rmq_init()
{
    cin>>n;
    for(int i=1;i<=n;i++)
        scanf("%d",&a[i]);
    for(int j=1;(1<<j)<=n;j++)
        for(int i=1;i+(1<<j)-1<=n;i++)
            dp[i][j]=min(dp[i][j-1],dp[i+(1<<j-1)][j-1]);
}

查询
令k=log2(r-l+1)
则区间[l,r]的最小值为min(dp[l][k],dp[r-(1<<k)+1][k])
下面是代码：
int rmq(int l,int r)
{
    int k=log2(r-l+1);
    return min(dp[l][k],dp[r-(1<<k)+1][k]);
}
