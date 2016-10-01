# 一个 Big-O notation简短介绍

知道一种算法的速度是蜗牛还是火箭，以及需要多少的space十分有用。它指引你去选择正确的算法。

Big-O notation粗犷的描述了一个算法在运行时间以及内存消耗。某路大牛（路人）：最糟糕的运行时间为**O(n^2)** 并且会用了**O(n)**的space“ 意味着，这只狗（算法）虽然走路（运行速度）慢了些，但是狗长得小(占用内存少)，姑且算它一条良狗吧。

计算一个算法的Big-O需要一些数学分析，这里我们就先略过了。知道各个不同值之间的区别意味着什么很重要，所以，当当当，请看表格！

n代表的是你索要运行的data item数目，如果你要排列100个数组，那么n=100

| Big-O          | Name           | Description                              |
| -------------- | -------------- | ---------------------------------------- |
| **O(1)**       | constant       | **This is the best神狗.** 无论数据有多大，运行时间都是相同的。例如，通过Index查找一个array中的数据 |
| **O(log n)**   | logarithmic    | **Pretty great天狗.** These kinds of algorithms halve the amount of data with each iteration.  .100个数据，找到仅需要7步，1000个数据只需要10步，1百万个数据仅需要20步，即使是超级大的狗也跑得快。 Example: binary search. |
| **O(n)**       | linear         | **Good performance 牛狗.** 例如: sequential search. |
| **O(n log n)** | "linearithmic" | **Decent performance好狗.**  Example: the fastest general-purpose sorting algorithms. |
| **O(n^2)**     | quadratic      | **Kinda slow狗.** 100个数据，要运行100*100次. Example: algorithms using nested loops, such as insertion sort. |
| **O(n^3)**     | cubic          | **Poor performance老狗.** 10个数据，运行10^3次. Example: matrix multiplication. |
| **O(2^n)**     | exponential    | **Very poor performance.病狗** 尽量避免，如果避免不了的话： just one bit to the input doubles the running time. Example: traveling salesperson problem. |
| **O(n!)**      | factorial      | **Intolerably slow.死狗** It literally takes a million years to do anything. |

Often you don't need math to figure out what the Big-O of an algorithm is but you can simply use your intuition. If your code uses a single loop that looks at all **n** elements of your input, the algorithm is **O(n)**. If the code has two nested loops, it is **O(n^2)**. Three nested loops gives **O(n^3)**, and so on.

通常你不需要数学知识去算出算法的Big-O，可以凭你的直觉。一个n个元素的循环，那就是O(n) 如果有两个嵌套的循环，就是O(n^2)..依次循环。。。五环啊五环，O（n^2）你只比O（n）多一环。

Big-O仅仅是用于预估的n值比较大的时候。实际上，与n的取值范围有关系，高中数学知识点，设个y，画个图就知道了。

如果你很困惑，不要慌，现在空狗无凭（没有算法）来比较，只有有了实际的两个算法来进行比对的时候才有意义。数据很少的时候，即使是最笨的算法也可以运行的很快。

原文具体解释如下：

Note that Big-O notation is an estimate and is only really useful for large values of **n**. For example, the worst-case running time for the [insertion sort](Insertion Sort/) algorithm is **O(n^2)**. In theory that is worse than the running time for [merge sort](Merge Sort/), which is **O(n log n)**. But for small amounts of data, insertion sort is actually faster, especially if the array is partially sorted already!

If you find this confusing, don't let this Big-O stuff bother you too much. It's mostly useful when comparing two algorithms to figure out which one is better. But in the end you still want to test in practice which one really is the best. And if the amount of data is relatively small, then even a slow algorithm will be fast enough for practical use.