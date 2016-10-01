# Algorithm design techniques

What to do when you're faced with a new problem and you need to find an algorithm for it.

当你碰到遭遇战，遇到新的问题的时候，需要一种的算法的时候，该怎么办？

### 有没有遭遇过类似的问题

如果将你碰到的新怪物（新问题）重新打磨成其他的更简单的问题。那么你将可以使用一个已经存在的算法，不要重复造轮子了。

- 干死过一头蛇，遭遇了九头蛇的话，别慌，砍成九条，一条一条对付就好了嘛

我觉得Steven Skiena比较屌的一点在于：他的 [The Algorithm Design Manual](http://www.algorist.com)   包含了一些问题的分类以及解决方式，以及他的仓库 [algorithm repository](http://www3.cs.stonybrook.edu/~algorith/) 



### 刚开始采用brute force的算法是可以接受

虽然Naive，brute force的解决方式很慢，但是在这里的关键是在于明白它的问题到底是什么？

粗糙的实现算法可以让你明确知道自己的对算法实现的提高在哪！

但是需要注意不要过早的优化算法。



### Divide and conquer

很多难以解决的大怪物，我们只需要将它拆分成一个一个小怪物干掉就好了。

