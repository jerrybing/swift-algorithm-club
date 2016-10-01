# Insertion Sort

终极目标：把一个array从低到高或者从高到低排序

给你一组含有数组的Array，并且需要你将它们进行正确的排序。插入排序法是这么插的:

- Put the numbers on a pile. This pile is unsorted.
- 将数字放到还没有被排序的pile(堆)上
- 从pile上选择一个数字，具体选择哪一个并不重要，最简单就是取第一个数字
- 把得到的数字插入别一个新的Array上
- 再次pile上获取下一个数字，同时插入到新的array中取，或是在新array中的前面或者后边，对于新的array，你现在拥有了两个已经排好序的数字
- 重复以上的逻辑一直到pile上没有数字了，此时你就获得了一个被排列好的数组了

现在你知道为啥子要叫做插入排序法了吧，因为你从再pile上的Array获取一个数字，然后正确的插入到另外一的Array上

## 一个小例子

好了，现在假设正在pile上准备让我们蹂躏的未排序数组是 `[ 8, 3, 5, 4, 6 ]`. 

选择第一个数字, `8`, 插入到新的Array中去. `[ 8 ]`   pile 上的array此时为 `[ 3, 5, 4, 6 ]`.

继续从pile上获取元素3，然后插入到8的前面，所以现在新的Array为【3，8】，而pile为【5，4，6】

再获取元素 `5`, 插入到新的Array中 `[ 3, 5, 8 ]` and the pile is `[ 4, 6 ]`.

重复，一直到pile上的array为空。

## In-place sort

上面的解释说明你需要两个Array，一个为未被排序的Array 另一个排序的Array

其实不一定非要创建分离的两个array，你可以跟踪当前这个array那一部分是进行排序了的，哪一部分是没有进行排序的。

Initially, the array is `[ 8, 3, 5, 4, 6 ]`.  `|`  表示排序部分的结束，以及仍然在pile上的不忍

	[| 8, 3, 5, 4, 6 ]
刚开始的时候，已经排序了的部分为空，pile开始为 `8`.

After processing the first number, we have:

党第一个数组完成排序之后

	[ 8 | 3, 5, 4, 6 ]

已经排序的部分为 `[ 8 ]`  pile 上的部分为 `[ 3, 5, 4, 6 ]`.  `|` bar向右移动

以下展示了array里的content在排序过程中变化

	[| 8, 3, 5, 4, 6 ]
	[ 8 | 3, 5, 4, 6 ]
	[ 3, 8 | 5, 4, 6 ]
	[ 3, 5, 8 | 4, 6 ]
	[ 3, 4, 5, 8 | 6 ]
	[ 3, 4, 5, 6, 8 |]

In each step, the `|` bar moves up one position. As you can see, the beginning of the array up to the `|` is always sorted. The pile shrinks by one and the sorted portion grows by one, until the pile is empty and there are no more unsorted numbers left.



## 如何插入

每一步你都要将未排序的部分的最顶部的数字插入到已经排序了的array里去。

假设我们已经完成了几个数字的排序，如：

	[ 3, 5, 8 | 4, 6 ]
 接下来就是将`4`插入到.`[ 3, 5, 8 ]`的某个地方



这里有一种方式来实现它： 

	[ 3, 5, 8, 4 | 6 ]
	        ^

4比8小，两个对换

	[ 3, 5, 4, 8 | 6 ]
	        <-->
	       swapped

5依旧大于4，再次对换

	[ 3, 4, 5, 8 | 6 ]
	     <-->
	    swapped

3月4不需要再次对换了。

以上只是inner loop of the insertion sort algorithm的一个描述。

## The code

Here is an implementation of insertion sort in Swift:

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array                             // 1 我们不能直接改变参数，需要先copy一下
  for x in 1..<a.count {                    // 2
    var y = x
    while y > 0 && a[y] < a[y - 1] {        // 3
      swap(&a[y - 1], &a[y])
      y -= 1
    }
  }
  return a
}
```

Put this code in a playground and test it like so:

```swift
let list = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(list)
```

Here is how the code works.

1. Make a copy of the array. This is necessary because we cannot modify the contents of the `array` parameter directly. Like Swift's own `sort()`, the `insertionSort()` function will return a sorted *copy* of the original array.

2. There are two loops inside this function. The outer loop looks at each of the elements in the array in turn; this is what picks the top-most number from the pile. The variable `x` is the index of where the sorted portion ends and the pile begins (the position of the `|` bar). Remember, at any given moment the beginning of the array -- from index 0 up to `x` -- is always sorted. The rest, from index `x` until the last element, is the unsorted pile.

3. The inner loop looks at the element at position `x`. This is the number at the top of the pile, and it may be smaller than any of the previous elements. The inner loop steps backwards through the sorted array; every time it finds a previous number that is larger, it swaps them. When the inner loop completes, the beginning of the array is sorted again, and the sorted portion has grown by one element.

> **Note:** The outer loop starts at index 1, not 0. Moving the very first element from the pile to the sorted portion doesn't actually change anything, so we might as well skip it. 

## No more swaps

The above version of insertion sort works fine, but it can be made a tiny bit faster by removing the call to `swap()`. 

You've seen that we swap numbers to move the next element into its sorted position:

	[ 3, 5, 8, 4 | 6 ]
	        <-->
	        swap
	        
	[ 3, 5, 4, 8 | 6 ]
	     <-->
	     swap

Instead of swapping with each of the previous elements, we can just shift all those elements one position to the right, and then copy the new number into the right position.

	[ 3, 5, 8, 4 | 6 ]   remember 4
	           *
	
	[ 3, 5, 8, 8 | 6 ]   shift 8 to the right
	        --->
	        
	[ 3, 5, 5, 8 | 6 ]   shift 5 to the right
	     --->
	     
	[ 3, 4, 5, 8 | 6 ]   copy 4 into place
	     *

In code that looks like this:

```swift
func insertionSort(_ array: [Int]) -> [Int] {
  var a = array
  for x in 1..<a.count {
    var y = x
    let temp = a[y]
    while y > 0 && temp < a[y - 1] {
      a[y] = a[y - 1]                // 1
      y -= 1
    }
    a[y] = temp                      // 2
  }
  return a
}
```

The line at `//1` is what shifts up the previous elements by one position. At the end of the inner loop, `y` is the destination index for the new number in the sorted portion, and the line at `//2` copies this number into place.

## Making it generic

It would be nice to sort other things than just numbers. We can make the datatype of the array generic and use a user-supplied function (or closure) to perform the less-than comparison. This only requires two changes to the code.

The function signature becomes:

```swift
func insertionSort<T>(_ array: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
```

The array has type `[T]` where `T` is the placeholder type for the generics. Now `insertionSort()` will accept any kind of array, whether it contains numbers, strings, or something else.

The new parameter `isOrderedBefore: (T, T) -> Bool` is a function that takes two `T` objects and returns true if the first object comes before the second, and false if the second object should come before the first. This is exactly what Swift's built-in `sort()` function does.

The only other change is in the inner loop, which now becomes:

```swift
      while y > 0 && isOrderedBefore(temp, a[y - 1]) {
```

Instead of writing `temp < a[y - 1]`, we call the `isOrderedBefore()` function. It does the exact same thing, except we can now compare any kind of object, not just numbers.

To test this in a playground, do:

```swift
let numbers = [ 10, -1, 3, 9, 2, 27, 8, 5, 1, 3, 0, 26 ]
insertionSort(numbers, <)
insertionSort(numbers, >)
```

The `<` and `>` determine the sort order, low-to-high and high-to-low, respectively.

Of course, you can also sort other things such as strings,

```swift
let strings = [ "b", "a", "d", "c", "e" ]
insertionSort(strings, <)
```

or even more complex objects:

```swift
let objects = [ obj1, obj2, obj3, ... ]
insertionSort(objects) { $0.priority < $1.priority }
```

The closure tells `insertionSort()` to sort on the `priority` property of the objects.

Insertion sort is a *stable* sort. A sort is stable when elements that have identical sort keys remain in the same relative order after sorting. This is not important for simple values such as numbers or strings, but it is important when sorting more complex objects. In the example above, if two objects have the same `priority`, regardless of the values of their other properties, those two objects don't get swapped around.

## Performance

Insertion sort is really fast if the array is already sorted. That sounds obvious, but this is not true for all search algorithms. In practice, a lot of data will already be largely -- if not entirely -- sorted and insertion sort works quite well in that case.

The worst-case and average case performance of insertion sort is **O(n^2)**. That's because there are two nested loops in this function. Other sort algorithms, such as quicksort and merge sort, have **O(n log n)** performance, which is faster on large inputs.

Insertion sort is actually very fast for sorting small arrays. Some standard libraries have sort functions that switch from a quicksort to insertion sort when the partition size is 10 or less.

I did a quick test comparing our `insertionSort()` with Swift's built-in `sort()`. On arrays of about 100 items or so, the difference in speed is tiny. However, as your input becomes larger, **O(n^2)** quickly starts to perform a lot worse than **O(n log n)** and insertion sort just can't keep up.

## See also

[Insertion sort on Wikipedia](https://en.wikipedia.org/wiki/Insertion_sort)

*Written for Swift Algorithm Club by Matthijs Hollemans*
