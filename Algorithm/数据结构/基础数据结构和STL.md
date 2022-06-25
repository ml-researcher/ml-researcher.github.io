# 基础数据结构和STL

## 链表

* [例题](http://acm.hdu.edu.cn/showproblem.php?pid=6375)
	* [AC代码](http://lvqingsong.info/pastebin/view/47f96d52)

这道题可以用启发式合并，但是最正解的还是直接用链表。

## queue和priority_queue


* [例题1 - queue](https://vjudge.net/problem/UVA-540)
* [例题2 - priority\_queue](https://vjudge.net/problem/UVA-136)：每次从队列取一个最小的，把它\*2，\*3，\*5入队。

## set和map

* [例题](https://vjudge.net/problem/UVA-12096)
	* [AC代码](http://lvqingsong.info/pastebin/view/4c5d2446)

可以说是把STL用到极致的一个例题了，顺便学习一下如何用C++取两个set的交和并。

还有一个类似的例题，也是这种把复杂结构映射到ID的方法：[UVA-1592](https://vjudge.net/problem/UVA-1592)。

## bitset

\_Find\_first()

\_Find\_next()

reset()

count()

## 参考资料

* https://book.douban.com/subject/25902102/

## Python

python里面很有用的包：

* collections
* sortedcontainers
* itertools.permutations
    * https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/
    * 复习一下c++的next_permutation