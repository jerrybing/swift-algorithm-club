# Stack

A stack is like an array but with limited functionality. You can only *push* to add a new element to the top of the stack, *pop* to remove the element from the top, and *peek* at the top element without popping it off.

一个stack就像是一个被阉割了的Array。你只能在stack的最顶部添加或者移除元素，或者偷窥一下最顶层的元素，但是不移除它。

- 比如，找苍老师要签名，排着队要签名，对于苍老师而言，只能从最头牌的那个开始，一个一个签。苍老师不可能突然冲入人群中签名，因为那样的话，苍老师冲入一群屌丝男的人群中的话？
- 比如脱泷泽萝拉的衣服，从外边一直外里边一件一件的脱，你不可能直接脱最里面的bra对吧！最外边一件可以看。不用非得脱掉

为什么又要作死，弄一个新的类型出来呢？因为在很多算法中，你只想添加一个object到一个临时list中，在晚一些时候，你又要把这个Object拿出来，添加和移除Object的顺序对于某个算法而言很重要。

- 给泷泽萝拉老师穿衣服，先穿一件，不喜欢，脱掉，再穿，再脱，再脱，再穿。顺序之所以重要是因为罗泽罗拉老师各个穿衣服的组合对于我而言很重要。





一个stack给了你一种称之为 **LIFO or last-in first-out order.**跟**queue（FIFO or first-in first-out 先进先出）**很像

stack的是最后进去的却是第一个出来的。

- 我们给龙泽老师最后穿上的衣服，必然是下一件最先脱下来的衣服

例如：

```swift
//push进去一个10的元素
stack.push(10)
```

stack 为： `[ 10 ]`. 

```swift
stack.push(3)
```

stack 为： `[ 10, 3 ]`. 

```swift
stack.push(57)
```

The stack is now `[ 10, 3, 57 ]`. Let's pop the top number off the stack:

```swift
stack.pop()//返回57，并且移除掉57
```

. The stack is `[ 10, 3 ]` 

```swift
stack.pop()//返回3
```

一直到 stack ：returns `nil` or 其他实现时候所发生的错误

好在在swift中实现stack很简单

```swift
public struct Stack<T> {
  fileprivate var array = [T]()

  public var isEmpty: Bool {
    return array.isEmpty
  }

  public var count: Int {
    return array.count
  }

  public mutating func push(_ element: T) {
    array.append(element)
  }

  public mutating func pop() -> T? {
    return array.popLast()
  }

  public func peek() -> T? {
    return array.last
  }
}
```

Notice that a push puts the new element at the end of the array, not the beginning. Inserting at the beginning of an array is expensive, an **O(n)** operation, because it requires all existing array elements to be shifted in memory. Adding at the end is **O(1)**; it always takes the same amount of time, regardless of the size of the array.

注意一下 push 将新元素添加到array的最后，而不是最开始的地方。如果插入到第一个的话，就是O(n)的复杂度了，所有的元素都会在内存中shift。但是插到最后的话，就你一个。O(1)的复杂度【ps：想想一下，有n个人等待苍老师签名的屌丝男，你排在最后，想第一签名，你要打死n-1个人，你要消耗多少内存和力气】



当当当！快结束了，来一个小知识点好了！

关于stacks的有趣小知识点：

每一次你调用一个方法或者函数，CPU将返回的address放在一个stack上，当你函数结束的时候，将这个addre放回给调用的位置。这就是为什么当你调用太多函数的时候， —例如调用 recursive function【死递归函数】的时候，恭喜你中了CPU颁发的stack overflow【栈溢出】成就，因为你消耗掉了所有的空间了，CPU的stack没有space了。

- 这个哪里有趣了，stack overflow 会有错误还有趣。。大牛的世界，小白表示不懂

> Fun fact about stacks: Each time you call a function or a method, the CPU places the return address on a stack. When the function ends, the CPU uses that return address to jump back to the caller. That's why if you call too many functions -- for example in a recursive function that never ends -- you get a so-called "stack overflow" as the CPU stack has run out of space.

*作者：Matthijs Hollemans；译者：Jerry Bing*