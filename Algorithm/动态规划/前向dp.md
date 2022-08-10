# 前向dp

之前经常遇到且大概率想不出来的题，在这里总结一下。一般的dp都是for一遍，for到谁就算谁：

```cpp
for (int i=1; i<=n; i++) dp[i] = xxxx
```

但是有的dp是for到谁用它去算谁：

```cpp
for (int i=1; i<=n; i++) dp[xxx] = xxx dp[i] xxx
```

我把这种dp叫做前向dp，因为没有在其他资料里找到，所以只能自己总结了。

* [例题](http://poj.org/problem?id=3661)
	* [AC代码](http://lvqingsong.info/pastebin/view/de6fa190)

题目暂时就找到这一个，以后遇到再补充吧。

* https://leetcode-cn.com/problems/frog-jump/
* https://leetcode.cn/problems/number-of-people-aware-of-a-secret/