在学习单调队列前，让我们先来看一道例题。

## 例题

[Sliding Window](http://poj.org/problem?id=2823)

本题大意是给出一个长度为 $n$ 的数组，编程输出每 $k$ 个连续的数中的最大值和最小值。

最暴力的想法很简单，对于每一段 $i \sim i+k-1$ 的序列，逐个比较来找出最大值（和最小值），时间复杂度约为 $O(n \times k)$ 。

很显然，这其中进行了大量重复工作，除了开头 $k-1$ 个和结尾 $k-1$ 个数之外，每个数都进行了 $k$ 次比较，而题中 $100\%$ 的数据为 $n \le 1000000$ ，当 $k$ 稍大的情况下，显然会 TLE。

这时所用到的就是单调队列了。

## 概念

顾名思义，单调队列的重点分为 "单调" 和 "队列"

"单调" 指的是元素的的 "规律"——递增（或递减）

"队列" 指的是元素只能从队头和队尾进行操作

Ps. 单调队列中的 "队列" 与正常的队列有一定的区别，稍后会提到

## 例题分析

有了上面 "单调队列" 的概念，很容易想到用单调队列进行优化。

要求的是每连续的 $k$ 个数中的最大（最小）值，很明显，当一个数进入所要 "寻找" 最大值的范围中时，若这个数比其前面（先进队）的数要大，显然，前面的数会比这个数先出队且不再可能是最大值。

也就是说——当满足以上条件时，可将前面的数 "弹出"，再将该数真正 push 进队尾。

这就相当于维护了一个递减的队列，符合单调队列的定义，减少了重复的比较次数，不仅如此，由于维护出的队伍是查询范围内的且是递减的，队头必定是该查询区域内的最大值，因此输出时只需输出队头即可。

显而易见的是，在这样的算法中，每个数只要进队与出队各一次，因此时间复杂度被降到了 $O(N)$ 。

而由于查询区间长度是固定的，超出查询空间的值再大也不能输出，因此还需要 site 数组记录第 $i$ 个队中的数在原数组中的位置，以弹出越界的队头。

??? "例题参考代码"

    ```cpp
    #include<cstdio>
    #include<iostream>
    #include<cstring>
    #include<cstdlib>
    #define maxn 1000100
    using namespace std;
    int q[maxn],a[maxn];
    int n,k;
    void getmin(){
        int head=0,tail=0;
        for (int i=1;i<k;i++){
            while (head<=tail&&a[q[tail]]>=a[i]) tail--;
            q[++tail]=i;
        }
        for (int i=k;i<=n;i++){
            while (head<=tail&&a[q[tail]]>=a[i]) tail--;
            q[++tail]=i;
            while (q[head]<=i-k) head++;
            printf("%d ",a[q[head]]);
        }
    }

    void getmax(){
        int head=0,tail=0;
        for (int i=1;i<k;i++){
            while (head<=tail&&a[q[tail]]<=a[i]) tail--;
            q[++tail]=i;
        }
        for (int i=k;i<=n;i++){
            while (head<=tail&&a[q[tail]]<=a[i]) tail--;
            q[++tail]=i;
            while (q[head]<=i-k) head++;
            printf("%d ",a[q[head]]);
        }
    }

    int main(){
        scanf("%d%d",&n,&k);
        for (int i=1;i<=n;i++) scanf("%d",&a[i]);
        getmin();
        printf("\n");
        getmax();
        printf("\n");
        return 0;
    }
    ```

Ps. 此处的 "队列" 跟普通队列的一大不同就在于可以从队尾进行操作，STL 中有类似的数据结构 deque。
