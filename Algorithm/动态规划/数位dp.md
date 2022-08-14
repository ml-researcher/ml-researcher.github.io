# 数位dp

## 基本思想

数位DP是动态规划里面一种很特殊的形式，因为我对于其他动态规划问题习惯使用迭代的形式，但是对于数位DP更喜欢使用递归的形式，所以对于这种DP单拎出来写一下。

其实DP是一种比较玄妙的思想，很难用一种套路的方式表达出来，关键是那种对于给定问题如何去定义“状态”的那种感觉。数位DP其实也是如此，只不过它的状态一般不是那么直接，而是需要一步转化。因为通常的DP都是定义好状态，最后dp[xxx]就是结果，但是数位DP是借助dp的状态数组来得到最终的结果。

通常来说问题是这样的：给定一个条件，问[L, R]之间满足条件的整数有多少个。

最暴力的方式当然是这样：

```cpp
for (int i=L; i<=R; i++) {
    if (judge(i)) ans ++;
}
```

数位DP的方法当然不会这么暴力，它通常会定义一个状态数组dp[pos][state]表示到第pos位开始可以随便放数字了，并且此时前面的状态是state（state这里可以是多维），满足条件的数字有多少个。

这个dp数组可以迭代得到，但是我觉得记忆化搜索的形式更好理解一些，所以这个dp数组通常是dfs得到，这个dfs函数的参数除了pos和state以外，还会设置一个全局变量数组b[]，表示要解决的问题的右边界。此外，还要加一个preok，表示是否可以开始随便放数字的状态。如果前面的已经比右边界小了，后面就可以随便放了，记录dp数组的时候只记录这些可以随意放的即可，因为不随意放的那些可以通过dfs得到。

## 例1：不要62

[题目链接](http://acm.hdu.edu.cn/showproblem.php?pid=2089)

这个是最基础的数位DP题。

```cpp
#include <cstdio>
#include <cstring>
using namespace std;

int a[10];
int dp[10][2];

int dfs(int pos, int pre_small, int pre6)
{
    if (pos < 0) return 1;
    if (pre_small && dp[pos][pre6] != -1) return dp[pos][pre6]; 
    int up = pre_small ? 9 : a[pos];
    int ans = 0;
    for (int i=0; i<=up; i++) {
        if (i != 4 && !(pre6 && i == 2)) {
            ans += dfs(pos-1, pre_small || i < a[pos], i == 6);
        }
    }
    if (pre_small) dp[pos][pre6] = ans;
    return ans;
}

int handle(int num)
{
    int p = 0;
    do {
        a[p++] = num % 10;
        num /= 10;
    } while (num);
    return dfs(p-1, false, false);
}

void init()
{
    memset(dp, -1, sizeof(dp));
}

int main()
{
    while (true) {
        int n, m;
        scanf("%d%d", &n, &m);
        if (n == 0 && m == 0) break;
        init();
        int ans = handle(m) - handle(n-1);
        printf("%d\n", ans);
    }
    return 0;
}
```

## 例2：F(x)

[题目链接](http://acm.hdu.edu.cn/showproblem.php?pid=4734)

最直接的数位DP，就是求[0, n]之间有多少个满足条件的。1e9实际上是个幌子，F(A)不会超过5000。

[AC代码](http://lvqingsong.info/pastebin/view/fb5e866f)

## 例3：Round Numbers

[题目链接](http://poj.org/problem?id=3252)

通过这个题可以对前导0的处理有新的理解，因为前导0会影响0的个数的计数，因此需要多记一个状态。

[AC代码](http://lvqingsong.info/pastebin/view/50a71cd7)

## 例4：B-Number

[题目链接](http://acm.hdu.edu.cn/showproblem.php?pid=3652)

这里记状态的时候用到了取模的数学性质（可加性和可乘性），因此多记录一个余数状态即可。

[AC代码](http://lvqingsong.info/pastebin/view/f7bfc3c1)

## 例5：Pinary

[题目链接](https://vjudge.net/problem/38405)

这个题跟常规的数位DP正好是反着的，一般都是给定区间问有多少个，这个题是给定有多少个，求区间。拍脑袋一想，二分即可。好在这个题可以在long long的范围内二分出来，不然要用大数二分就比较麻烦了。（要是真要用大数二分我选择用Python……）

[AC代码](http://lvqingsong.info/pastebin/view/e3e36745)

## 例6：Balanced Number

[题目链接](http://acm.hdu.edu.cn/showproblem.php?pid=3709)

有时候要去证明一个东西是不容易的，比如我想证明“一个数至多只有一个pivot位置”，从数学上很难证明。但是我们可以去理解，类比天平重心。每个数位看做一个质量，一根天平杆重心只有一个。

[AC代码](http://lvqingsong.info/pastebin/view/3dbbae4e)

## 总结

懂了以上例题，数位DP基本上就算是掌握了，关键就在于理解好这种思想的套路，然后具体问题具体分析。还是强调那种对于“状态”的感觉。

参考资料：https://www.bilibili.com/video/BV1ip4y1v7zw

## 例7

https://leetcode.cn/problems/count-special-integers/

为什么有种不知道怎么回事就过了的感觉……能写出来，但还是不得要领。


```cpp
#include <cstdio>
#include <cstring>
using namespace std;

int a[10];
int dp[10][2048][2];

int dfs(int pos, int pre_small, int pre_state, int pre0)
{
    if (pos < 0) return 1;
    if (pre_small && dp[pos][pre_state][pre0] != -1) return dp[pos][pre_state][pre0];
    int up = pre_small ? 9 : a[pos];
    int ans = 0;
    for (int i=0; i<=up; i++) {
        if (((pre_state>>i)&1) == 0 || pre0) {
            int new_state = pre0 && i == 0 ? pre_state : pre_state | (1<<i);
            ans += dfs(pos-1, pre_small || i < a[pos], new_state, pre0 && i == 0);
        }
    }
    if (pre_small) dp[pos][pre_state][pre0] = ans;
    return ans;
}

int handle(int num)
{
    int p = 0;
    do {
        a[p++] = num % 10;
        num /= 10;
    } while (num);
    return dfs(p-1, false, false, true);
}

void init()
{
    memset(dp, -1, sizeof(dp));
}

int main()
{
    while (true) {
        int n;
        scanf("%d", &n);
        if (n == 0) break;
        init();
        int ans = handle(n);
        printf("%d\n", ans);
    }
    return 0;
}
```