# Binary Tree

A binary tree is a [tree](../Tree/) where each node has 0, 1, or 2 children. 

如：

![A binary tree](Images/BinaryTree.png)



子节点通常被称为left child 和right child，如果一个node没有任何的children，叫做一个leaf node。，root是最为顶端的node。【编程的世界里，我们喜欢它们上下对吊着看】

Often nodes will have a link back to their parent but this is not strictly necessary.

node通常都有一种连接回它们的parent，但并未严格必要。

Binary trees are often used as [binary search trees](../Binary Search Tree/). In that case, the nodes must be in a specific order (smaller values on the left, larger values on the right). But this is not a requirement for all binary trees.

Binary trees 经常被用来  [binary search trees](../Binary Search Tree/).，在这种情况下，nodes必须有一个明确的排序，小的数值在左，大的在右，但是并不是所有binary tree都要求这个样子。



例如它表示一个一个运算式子 `(5 * (a - 10)) + (-4 * (3 / b))`:

![A binary tree](Images/Operations.png)

## The code

在swift中实现一个 general-purpose binary tree

```swift
public indirect enum BinaryTree<T> {
  case Node(BinaryTree<T>, T, BinaryTree<T>)
  case Empty
}
```

一个如何去使用它的例子，首先我们依据这个tree构建出来一个算数表达式

```swift
// leaf nodes
let node5 = BinaryTree.Node(.Empty, "5", .Empty)
let nodeA = BinaryTree.Node(.Empty, "a", .Empty)
let node10 = BinaryTree.Node(.Empty, "10", .Empty)
let node4 = BinaryTree.Node(.Empty, "4", .Empty)
let node3 = BinaryTree.Node(.Empty, "3", .Empty)
let nodeB = BinaryTree.Node(.Empty, "b", .Empty)

// intermediate nodes on the left
let Aminus10 = BinaryTree.Node(nodeA, "-", node10)
let timesLeft = BinaryTree.Node(node5, "*", Aminus10)

// intermediate nodes on the right
let minus4 = BinaryTree.Node(.Empty, "-", node4)
let divide3andB = BinaryTree.Node(node3, "/", nodeB)
let timesRight = BinaryTree.Node(minus4, "*", divide3andB)

// root node
let tree = BinaryTree.Node(timesLeft, "+", timesRight)
```

你需要从反向的构建这个树，从leaf node开始 一直到root node

It will be useful to add a `description` method so you can print the tree:

增加一个`description` 方法来阐述tree

```swift
extension BinaryTree: CustomStringConvertible {
  public var description: String {
    switch self {
    case let .Node(left, value, right):
      return "value: \(value), left = [" + left.description + "], right = [" 
                                         + right.description + "]"
    case .Empty:
      return ""
    }
  }
}
```

 `print(tree)` 可以看到：

	value: +, left = [value: *, left = [value: 5, left = [], right = []], right = [value: -, left = [value: a, left = [], right = []], right = [value: 10, left = [], right = []]]], right = [value: *, left = [value: -, left = [], right = [value: 4, left = [], right = []]], right = [value: /, left = [value: 3, left = [], right = []], right = [value: b, left = [], right = []]]]
发挥想象力的话，你可以想象成树的样子。缩进一下，很有帮助。

	value: +, 
		left = [value: *, 
			left = [value: 5, left = [], right = []], 
			right = [value: -, 
				left = [value: a, left = [], right = []], 
				right = [value: 10, left = [], right = []]]], 
		right = [value: *, 
			left = [value: -, 
				left = [], 
				right = [value: 4, left = [], right = []]], 
			right = [value: /, 
				left = [value: 3, left = [], right = []], 
				right = [value: b, left = [], right = []]]]

Another useful method is counting the number of nodes in the tree:

另外很有用处的用来count tree上的nodes

```swift
  public var count: Int {
    switch self {
    case let .Node(left, _, right):
      return left.count + 1 + right.count
    case .Empty:
      return 0
    }
  }
```

On the tree from the example, `tree.count` should be 12.

Something you often need to do with trees is traverse them, i.e. look at all the nodes in some order. There are three ways to traverse a binary tree:

1. *In-order* (or *depth-first*): first look at the left child of a node, then at the node itself, and finally at its right child.
2. *Pre-order*: first look at a node, then at its left and right children. 
3. *Post-order*: first look at the left and right children and process the node itself last.

Here is how you'd implement that:

```swift
  public func traverseInOrder(process: (T) -> Void) {
    if case let .Node(left, value, right) = self {
      left.traverseInOrder(process: process)
      process(value)
      right.traverseInOrder(process: process)
    }
  }
  
  public func traversePreOrder(process: (T) -> Void) {
    if case let .Node(left, value, right) = self {
      process(value)
      left.traversePreOrder(process: process)
      right.traversePreOrder(process: process)
    }
  }
  
  public func traversePostOrder(process: (T) -> Void) {
    if case let .Node(left, value, right) = self {
      left.traversePostOrder(process: process)
      right.traversePostOrder(process: process)
      process(value)
    }
  }
```

As is common when working with tree structures, these functions call themselves recursively.

For example, if you traverse the tree of arithmetic operations in post-order, you'll see the values in this order:

	5
	a
	10
	-
	*
	4
	-
	3
	b
	/
	*
	+

The leaves appear first. The root node appears last.

You can use a stack machine to evaluate these expressions, something like the following pseudocode:

```swift
tree.traversePostOrder { s in 
  switch s {
  case this is a numeric literal, such as 5:
    push it onto the stack
  case this is a variable name, such as a:
    look up the value of a and push it onto the stack
  case this is an operator, such as *:
    pop the two top-most items off the stack, multiply them,
    and push the result back onto the stack
  }
  the result is in the top-most item on the stack
}
```

*Written for Swift Algorithm Club by Matthijs Hollemans*
