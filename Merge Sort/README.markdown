# Merge Sort

目标：sort一个array,从低到高，或者从高到低。

 1945 John von Neumann 提出！ merge sort在最好最差以及平均的复杂度为**O(n log n)**.



The idea behind merge sort is to **divide and conquer**: to divide a big problem into smaller problems and solving many small problems instead of solving a big one. I think of merge sort as **split first** and **merge after**. 

merger sort的核心精华在于 divide and conquer || 分开 占领 。九头蛇一起打，打不过，那就一个头一个头的来打。将一个复杂的大问题转变为小的问题。简单的说merge sort就是先分开，然后再合体 as split first and merge after

给你一个装了n个数字的array，现在给你一个排序他们的任务。sort merge算法的是这样工作的。

- 把数字放到pile上，现在pile上的尚未排序
- 把pile的拆分为两半，现在你就拥有两个为排序的 array，在pile上
- 重复以上的操作，直到有了n个 pile上，每个pile只有一个数字
- 还是对每一个pile进行merge了，每一次的merge的同时，都进行相应的排序，这样的容易的原因是因为每一个独立的pile已经被排序了

## 例子

### 拆分Splitting

现在我们要去排序`【2，1，5，4，9】`，这是你尚未排序的pile，现在我们要做的就是拆分它知道你拆不了为止。

首先，你拆成了两个部分，可以继续拆 `[2, 1]` and `[5, 4, 9]`. 可以继续拆

我们先看左边的的部分 `[2, 1]` ， 可以继续拆分为`[2]` and `[1]`，此时无法继续拆分了

`[5, 4, 9]` => `[5]`  + `[4, 9]` 接着 `[4, 9]` => `[4]` + `[9]`. 

`【2，1，5，4，9】` => `[2]` `[1]` `[5]` `[4]` `[9]`. 

### Merging

拆分完之后，现在你可以对他们进行merge的同时并**排序**它们，还记得前面提到过的吗？我们要复杂的问题拆分为小的问题去解决，因为我们每一次的merge都**将只关注merge一个pile**

 `[2]` `[1]` `[5]` `[4]` `[9]`,=>`[1, 2]` and `[4, 5]` and `[9]`.

 因为 `[9]` is the odd one out, 所以在这个操作过程中你无法对其进行任何merge操作了。

下一个merge就是将`[1, 2]`  +`[4, 5] ` =>`[1, 2, 4, 5]`

你现在只有两个pile和【9】，此时终于可以对它进排序操作了 `[1, 2, 4, 5, 9]`. 

## Top-down implementation

Here's what merge sort may look like in Swift:

```swift
func mergeSort(_ array: [Int]) -> [Int] {
  guard array.count > 1 else { return array }    // 1

  let middleIndex = array.count / 2              // 2

  let leftArray = mergeSort(Array(array[0..<middleIndex]))             // 3

  let rightArray = mergeSort(Array(array[middleIndex..<array.count]))  // 4

  return merge(leftPile: leftArray, rightPile: rightArray)             // 5
}
```

A step-by-step explanation of how the code works:

1. If the array is empty or only contains a single element, there's no way to split it into smaller pieces. You'll just return the array.

2. Find the middle index. 

3. Using the middle index from the previous step, recursively split the left side of the array.

4. Also recursively split the right side of the array.

5. Finally, merge all the values together, making sure that it's always sorted.

Here's the merging algorithm:

```swift
func merge(leftPile: [Int], rightPile: [Int]) -> [Int] {
  // 1
  var leftIndex = 0
  var rightIndex = 0

  // 2 
  var orderedPile = [Int]()

  // 3
  while leftIndex < leftPile.count && rightIndex < rightPile.count {
    if leftPile[leftIndex] < rightPile[rightIndex] {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
    } else if leftPile[leftIndex] > rightPile[rightIndex] {
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    } else {
      orderedPile.append(leftPile[leftIndex])
      leftIndex += 1
      orderedPile.append(rightPile[rightIndex])
      rightIndex += 1
    }
  }

  // 4
  while leftIndex < leftPile.count {
    orderedPile.append(leftPile[leftIndex])
    leftIndex += 1
  }

  while rightIndex < rightPile.count {
    orderedPile.append(rightPile[rightIndex])
    rightIndex += 1
  }

  return orderedPile
}
```

This method may look scary but it is quite straightforward:

1. You need two indexes to keep track of your progress for the two arrays while merging.

2. This is the merged array. It's empty right now, but you'll build it up in subsequent steps by appending elements from the other arrays.

3. This while loop will compare the elements from the left and right sides, and append them to the `orderedPile` while making sure that the result stays in order.

4. If control exits from the previous while loop, it means that either `leftPile` or `rightPile` has its contents completely merged into the `orderedPile`. At this point, you no longer need to do comparisons. Just append the rest of the contents of the other array until there's no more to append.

As an example of how `merge()` works, suppose that we have the following piles: `leftPile = [1, 7, 8]` and `rightPile = [3, 6, 9]`. Note that each of these piles is individually sorted already -- that is always true with merge sort. These are merged into one larger sorted pile in the following steps:

	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ ]
	  l              r

The left index, here represented as `l`, points at the first item from the left pile, `1`. The right index, `r`, points at `3`. Therefore, the first item we add to `orderedPile` is `1`. We also move the left index `l` to the next item.

	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1 ]
	  -->l           r

Now `l` points at `7` but `r` is still at `3`. We add the smallest item to the ordered pile, so that's `3`. The situation is now:

	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3 ]
	     l           -->r

This process repeats. At each step we pick the smallest item from either `leftPile` or `rightPile` and add it to `orderedPile`:

	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6 ]
	     l              -->r
	
	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7 ]
	     -->l              r
	
	leftPile       rightPile       orderedPile
	[ 1, 7, 8 ]    [ 3, 6, 9 ]     [ 1, 3, 6, 7, 8 ]
	        -->l           r

Now there are no more items in the left pile. We simply add the remaining items from the right pile, and we're done. The merged pile is `[ 1, 3, 6, 7, 8, 9 ]`. 

Notice that this algorithm is very simple: it moves from left-to-right through the two piles and at every step picks the smallest item. This works because we guarantee that each of the piles is already sorted.

## Bottom-up implementation

The implementation of merge sort you've seen so far is called "top-down" because it first splits the array into smaller piles and then merges them. When sorting an array (as opposed to, say, a linked list) you can actually skip the splitting step and immediately start merging the individual array elements. This is called the "bottom-up" approach.

Time to step up the game a little. :-) Here is a complete bottom-up implementation in Swift:

```swift
func mergeSortBottomUp<T>(_ a: [T], _ isOrderedBefore: (T, T) -> Bool) -> [T] {
  let n = a.count

  var z = [a, a]      // 1
  var d = 0
    
  var width = 1
  while width < n {   // 2
  
    var i = 0
    while i < n {     // 3

      var j = i
      var l = i
      var r = i + width
      
      let lmax = min(l + width, n)
      let rmax = min(r + width, n)
      
      while l < lmax && r < rmax {                // 4
        if isOrderedBefore(z[d][l], z[d][r]) {
          z[1 - d][j] = z[d][l]
          l += 1
        } else {
          z[1 - d][j] = z[d][r]
          r += 1
        }
        j += 1
      }
      while l < lmax {
        z[1 - d][j] = z[d][l]
        j += 1
        l += 1
      }
      while r < rmax {
        z[1 - d][j] = z[d][r]
        j += 1
        r += 1
      }

      i += width*2
    }
    
    width *= 2
    d = 1 - d      // 5
  }
  return z[d]
}
```

It looks a lot more intimidating than the top-down version but notice that the main body includes the same three `while` loops from `merge()`.

Notable points:

1. Merge sort needs a temporary working array because you can't merge the left and right piles and at the same time overwrite their contents. But allocating a new array for each merge is wasteful. Therefore, we're using two working arrays and we'll switch between them using the value of `d`, which is either 0 or 1. The array `z[d]` is used for reading, `z[1 - d]` is used for writing. This is called *double-buffering*.

2. Conceptually, the bottom-up version works the same way as the top-down version. First, it merges small piles of 1 element each, then it merges piles of 2 elements each, then piles of 4 elements each, and so on. The size of the pile is given by `width`. Initially, `width` is `1` but at the end of each loop iteration we multiply it by 2. So this outer loop determines the size of the piles being merged. And in each step, the subarrays to merge become larger.

3. The inner loop steps through the piles and merges each pair of piles into a larger one. The result is written in the array given by `z[1 - d]`.

4. This is the same logic as in the top-down version. The main difference is that we're using double-buffering, so values are read from `z[d]` and written into `z[1 - d]`. It also uses an `isOrderedBefore` function to compare the elements rather than just `<`, so this merge sort is generic and you can use it to sort any kind of object you want.

5. At this point, the piles of size `width` from array `z[d]` have been merged into larger piles of size `width * 2` in array `z[1 - d]`. Here we swap the active array, so that in the next step we'll read from the new piles we've just created.

This function is generic, so you can use it to sort any type you desire, as long as you provide a proper `isOrderedBefore` closure to compare the elements.

Example of how to use it:

```swift
let array = [2, 1, 5, 4, 9]
mergeSortBottomUp(array, <)   // [1, 2, 4, 5, 9]
```

## Performance

The speed of merge sort is dependent on the size of the array it needs to sort. The larger the array, the more work it needs to do. 

Whether or not the initial array is sorted already doesn't affect the speed of merge sort since you'll be doing the same amount splits and comparisons regardless of the initial order of the elements.

Therefore, the time complexity for the best, worst, and average case will always be **O(n log n)**. 

A disadvantage of merge sort is that it needs a temporary "working" array equal in size to the array being sorted. It is not an **in-place** sort, unlike for example [quicksort](../Quicksort/).

Most implementations of merge sort produce a **stable** sort. This means that array elements that have identical sort keys will stay in the same order relative to each other after sorting. This is not important for simple values such as numbers or strings, but it can be an issue when sorting more complex objects.

## See also

[Merge sort on Wikipedia](https://en.wikipedia.org/wiki/Merge_sort)

*Written by Kelvin Lau. Additions by Matthijs Hollemans.*
