# Queue

queue是一个列表，你只能从后边添加一个新元素并且从最前面移除。保证了先来先上。先来的，先服务。

- 过安检的车，后边可以随时一个接一个的来，但是一定是最前面的那个先出去
- 想象一个大的钢管。一对小屁孩儿排队钻进去，先钻进去的，先钻出来。

需要这种queue的理由跟stack一样，你只想添加一个object到一个临时list中，在晚一些时候，你又要把这个Object拿出来，添加和移除Object的顺序对于某个算法而言很重要。

例如 enqueue 一个数字

```swift
queue.enqueue(10)
```

The queue is now `[ 10 ]`. 

```swift
queue.enqueue(3)
```

The queue is now `[ 10, 3 ]`.

```swift
queue.enqueue(57)
```

The queue is now `[ 10, 3, 57 ]`. 

我们现在移除掉 queue的第一个元素

```swift
queue.dequeue()
```

返回的 `10`就是我们想要的  The queue is now `[ 3, 57 ]`. 

每一个元素都向前移动了。

```swift
queue.dequeue()
```

This returns `3`, 下一个 returns `57`, and so on.

 如果 queue is empty, dequeuing returns `nil` or 或者其他实现的方式返回一个error

> **Note:** A queue is not always the best choice. If the order in which the items are added and removed from the list isn't important, you might as well use a [stack](../Stack/) instead of a queue. Stacks are simpler and faster.
>
> 注意：queue不总是最好的选择，如果item被添加或者被移除的顺序不是很重要的话，你可以使用stack取代queue，因为stack更快更简单



## The code

Here is a very simplistic implementation of a queue in Swift. It's just a wrapper around an array that lets you enqueue, dequeue, and peek at the front-most item:

这是采用Swift简单实现的queue，It's just a wrapper around an array that让你进行各种操作：enqueue，dequeue，peek最前面的那一个。

```swift
public struct Queue<T> {
  fileprivate var array = [T]()

  public var isEmpty: Bool {
    return array.isEmpty
  }
  
  public var count: Int {
    return array.count
  }

  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func dequeue() -> T? {
    if isEmpty {
      return nil
    } else {
      return array.removeFirst()
    }
  }
  
  public func peek() -> T? {
    return array.first
  }
}
```

This queue works just fine but it is not optimal.

虽然queue可以使用了，但是不是最佳方法



Enqueuing是一种**O(1)** 的操作，因为插入到array的最后一个元素，无论array的item数量有多少，都是一样的

为什么添加到Array的最后一个元素是O(1)呢？这个因为在Swift中 一个array总是在最后拥有一些space。

例如：

```swift
var queue = Queue<String>()
queue.enqueue("Ada")
queue.enqueue("Steve")
queue.enqueue("Tim")
```

实际上的array可以看做：

	[ "Ada", "Steve", "Tim", xxx, xxx, xxx ]

 `xxx`表示内存被保留但是还没有被填满。 添加一个新的元素之后

	[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
所以它仅仅是从内存中的某个地方直接拷贝过来。

当然， xxx是有限的，当最后一个xxx被使用掉的时候，需要resize array以获得更多空间

Resizing的时候，包括分配了内存并且复制原先的Array到新的Array中去，这个过程为相当慢的O(n),但是它只会**only every so often 偶尔出现**所以添加一个新元素到array上的时候还是平均O(1)或者说“**O(1)** "amortized"”

那么dequeue有些许不同，由于是获取的是array的第一个，所以是O(n)的复杂度

例如：下面的例子

	before   [ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
	                   /       /      /
	                  /       /      /
	                 /       /      /
	                /       /      /
	 after   [ "Steve", "Tim", "Grace", xxx, xxx, xxx ]

enqueuing高效，但是dequeuing不行 

## 一个更有效的queue

为了促使dequeuing更加高效，我们使用之前相同的track保留一些其他的space，但是这一次我们是添加到array的前面。swift内置的swift并没有为我们实现这样的功能，所以需要挽起袖子自己干！

- 为了queue，为了queen，为了国王。。。。。。。

The idea is as follows: whenever we dequeue an item, we don't shift the contents of the array to the front (slow) but mark the item's position in the array as empty (fast). After dequeuing `"Ada"`, the array is:

**这个简直是充满脑浆的智慧啊！**当我们dequeue一个item的时候，我们并不把array的content进行shift，因为它很慢，我们只把那个被dequeue的Item做出来一个**空的标记**。例如原来的Array中的第一个元素“Ada”被dequeue之后，如下

```swift
[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]
```

After dequeuing `"Steve"`, the array is:

	[ xxx, xxx, "Tim", "Grace", xxx, xxx ]
这些在前面用来做占位的xxx永远都不会再被用来做什么，所以浪费space。偶尔你可以通过移动剩下的Items来修整Array。

	[ "Tim", "Grace", xxx, xxx, xxx, xxx ]
修整Array的时候复杂度仍然为O(n)，但是它仅仅会偶尔发生，所以平均而言dequeue现在为O(1)

在领悟了改良版的脑髓之后，是时候喷出脑浆，来看看如何用swift具体来实现的吧。

```swift
public struct Queue<T> {
  fileprivate var array = [T?]()
  fileprivate var head = 0
  
  public var isEmpty: Bool {
    return count == 0
  }

  public var count: Int {
    return array.count - head
  }
  
  public mutating func enqueue(_ element: T) {
    array.append(element)
  }
  
  public mutating func dequeue() -> T? {
    guard head < array.count, let element = array[head] else { return nil }

    array[head] = nil
    head += 1

    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head)
      head = 0
    }
    
    return element
  }
  
  public func peek() -> T? {
    if isEmpty {
      return nil
    } else {
      return array[head]
    }
  }
}
```

你可以发现现在的Array里的元素从**T**更改为了**T？**，是因为我们需要用某种方式作  为mark来表示Array里某个元素为被清空了，head为Array最前面那个元素的index

Most of the new functionality sits in `dequeue()`. When we dequeue an item, we first set `array[head]` to `nil` to remove the object from the array. Then we increment `head` because now the next item has become the front one.

新添加的精华大多都给了dequeue（）,当我们dequeue一个item的时候，我们首先设置array[head]为nil，然后增加head的值，最前面的元素的index已经变为原来的1

从 =====>

```swift
[ "Ada", "Steve", "Tim", "Grace", xxx, xxx ]
  head
```

到===>

```swift
[ xxx, "Steve", "Tim", "Grace", xxx, xxx ]
        head
```

这个就好比某一个奇葩超市，排队的人不动，反而是收银台在对着顾客队列移动

但是没有我们没有把Array中的 “xxx”给移除掉的话，那么Array会随着我们的dequeue以及enqueue越来越大。为了周期性的修整Array我们进行了下面的优化

```swift
    let percentage = Double(head)/Double(array.count)
    if array.count > 50 && percentage > 0.25 {
      array.removeFirst(head)
      head = 0
    }
```

开始的时候计算出此时 被替换为’xxx‘元素如果超过50个并且超过了25%的话我们进行修整Array

> **Note:我只是凭空的确定了这些数字，你需要根据你的实际运行环境进行配置**.

 在 playground中来玩耍一下

```swift
var q = Queue<String>()
q.array                   // [] empty array

q.enqueue("Ada")
q.enqueue("Steve")
q.enqueue("Tim")
q.array             // [{Some "Ada"}, {Some "Steve"}, {Some "Tim"}]
q.count             // 3

q.dequeue()         // "Ada"
q.array             // [nil, {Some "Steve"}, {Some "Tim"}]
q.count             // 2

q.dequeue()         // "Steve"
q.array             // [nil, nil, {Some "Tim"}]
q.count             // 1

q.enqueue("Grace")
q.array             // [nil, nil, {Some "Tim"}, {Some "Grace"}]
q.count             // 2
```

To test the trimming behavior, replace the line,

```swift
    if array.count > 50 && percentage > 0.25 {
```

with:

```swift
    if head > 2 {
```

Now if you dequeue another object, the array will look as follows:

```swift
q.dequeue()         // "Tim"
q.array             // [{Some "Grace"}]
q.count             // 1
```

The `nil` objects at the front have been removed and the array is no longer wasting space. 在前面为nil的object被移除掉了并且array并没有浪费space，虽然这个版本的queue并没有比第一种版本的queue有多复杂，但是dequeuing 现在的复杂度也是 **O(1)** ，同志们，这就是知识的力量啊！

## See also：客官，请看这里还有呢

还有很多其他实现queue的方法，例如采用use a [linked list](../Linked List/), a [circular buffer](../Ring Buffer/), or a [heap](../Heap/). 

Variations on this theme are [deque](../Deque/), a double-ended queue where you can enqueue and dequeue at both ends, and [priority queue](../Priority Queue/), a sorted queue where the "most important" item is always at the front.

*作者： Matthijs Hollemans 翻译 :Jerry Bing*
