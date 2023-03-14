# webdataset

这里需要学习一下源码和python的一些编程技巧。

## class method chaining

一般来说就是return self，就可以给对象一直obj.func1().func2()...了。

https://www.tutorialspoint.com/Explain-Python-class-method-chaining

## Iterator

`__next__ StopIteration __iter__`可以构造一个itertator类。

另一个重要的概念：generator。yield

https://towardsdatascience.com/implementing-a-data-pipeline-by-chaining-python-iterators-b5887c2f0ec6

里面有一个有意思的例子就是可以递归地yield from。这样可以动态batch size。

webdataset利用了chaining+iterator这个技巧。