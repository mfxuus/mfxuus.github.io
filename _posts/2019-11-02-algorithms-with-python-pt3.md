---
title: "Algorithms with Python - Quick Sort and Heap Sort"
excerpt: "Explore the common sorting algorithms, the theory behind them, as well as how to implement them using python. Part 3. Quick Sort, Heap Sort."
<!-- last_modified_at: 2016-03-09T16:20:02-05:00 -->
header:
  image: /assets/images/headers/sorting.jpg
categories:
  - Algorithm Series
tags:
  - python
  - sorting algorithms
  - binary heap
toc: true
toc_h_max: 3
related: true
---


## Overview

In part 3 of our series, we will cover the following two algorithms:

- Quick Sort
- Heap Sort

Quick sort, similar to Merge Sort covered in Part 2, is also a divide-and-conquer algorithm. It has many variations in its implementation, resulting in a wide range of performances on different types of data; here in this blog we will cover an implementation that always chooses the last element as the pivot point (more details below).

Heap sort, on the other hand, will seem to be quite different from other algorithms we have discussed so far, since it mainly operates on a data structure named Binary Heap. We will go into details on these concepts, including the definitions, array representation of such heaps, and the actual implementation.

### Links to Other Parts of Series
- [Part 1: Bubble, Selection, and Insertion Sort]({% post_url 2019-10-19-algorithms-with-python-pt1 %})
- [Part 2: Merge and Tim Sort]({% post_url 2019-10-26-algorithms-with-python-pt2 %})

- [All Posts in Algorithms Series]({{ "Algorithm Series" | slugify | prepend: "#" | prepend: site.category_archive.path | relative_url }})

### Array Sorting Complexities

Recall the time complexities (for a full summary, see Part 1).

| Algorithm      |                  | Time Complexity   |                  |
|----------------|:----------------:|:-----------------:|:----------------:|
|                |       Best       |      Average      |       Worst      |
| Quick Sort     |    Ω(nlog(n))    |     θ(nlog(n))    | O(n<sup>2</sup>) |
| Heap Sort      |    Ω(nlog(n))    |     θ(nlog(n))    |    O(nlog(n))    |



## Sorting Algorithms

### Quick Sort

| ![quick-sort.gif](/assets/gifs/sorts/quick-sort.gif) |
|:--:|
| *(gif <a href="https://www.tutorialspoint.com/data_structures_algorithms/quick_sort_algorithm.htm">Credit</a>)* |


The basic procedure for Quick Sort is as follows:
1. Picking a pivot point from the array;
2. Perform a run through the array such that when the run is finished, all elements smaller than the pivot element is on the left of the pivot point, and similarly, all elements larger are on the right;
3. Perform steps 1 and 2 on the sub-arrays once on the left and once on the right, and keep doing so recursively until we get to the base case.

A few notes to keep in mind:
1. There are many ways to pick the pivot point - each might be better suited for a separate real world problem, but for us, we will simply pick the last element in the respective arrays as the pivot element;
2. Step 2 is usually called _partitioning_. There are also many variations on how the partitioning is done, and we will implement a most straightforward way to help illustrate the core algorithm.
3. The base case is when we get to a sub-array of length 0 or 1, in which case, we have two sub-arrays already in order, and we can build back up the entire array.


#### Python Implementation

```python
# quick sort
class QuickSort():

    """
    This time we implement a class to faciliate passing
    the target array around, and making the sort in-place.
    """

    def __init__(self, arr):
        """
        Initiate the object with a target array
        """
        self.arr = arr

    def partition(self, start, end):
        """
        Given an array arr,
        work on a sub-array defined by start and end.

        Pivot would be chosen from the end of the subarray.

        Returns the new pivot point,
        and modifies the array such that,
        within the sub-array, all values smaller
        then pivot is on the left, and all larger elements
        are on the right.

        Parts not in the sub-array are unmodified.

        """
        # get pivot
        # here always use the last element
        pivot = self.arr[end]
        # now just as the provided gif shows
        # we start scanning from the two ends of the sub array (excluding the pivot)
        # while left < pivot, left --> next on the right
        # while right > pivot, right --> next on the left
        # if we got left >= pivot, and right <= pivot, swap these two elements
        # when left meets right, insert the pivot into that location.
        left = start
        right = end - 1
        while left < right:
            while self.arr[left] < pivot:
                left += 1
            # after the loop above,
            # arr[left] >= pivot
            while self.arr[right] > pivot and left < right:
                right -= 1
            # get here, either we have a swap situation, or
            # left >= right and we can insert the pivot

            # left can > right, for example, if subarr = [1, 1, 1, 1, 5]
            # and the first while loop will result in left == end > right == end -1
            if left >= right:
                # insert pivot (swap last left with pivot)
                # since arr[left] >= pivot
                self.arr[end] = self.arr[left]
                self.arr[left] = pivot
            else:
                # else, it must be that
                # arr[left] >= pivot, and arr[right] <= pivot
                # and so left and right can swap
                self.arr[left], self.arr[right] = self.arr[right], self.arr[left]

            # print out the state of the array for visualization
            print(self.arr)

        # now sub array is partitioned,
        # and we can return the pivot point for next recursive partitioning
        return left

    def quick_sort(self, start, end):
        """
        performs quick sort on the subarray
        """
        # only do so if we have something to sort
        if start < end:
            # get new pivot point
            pivot_ind = self.partition(start, end)

            # perform the quick_sort on the two new sub arrays
            self.quick_sort(start, pivot_ind-1)
            self.quick_sort(pivot_ind+1, end)

    def sort(self):
        self.quick_sort(0, len(self.arr) - 1)
        return self.arr
```

Now let's test the code:

```python
# generate some random integers
import random
to_sort = random.sample(range(1, 1000), 20)

to_sort
# [630, 787, 118, 555, 509, 130, 74, 444, 583, 632, 891, 598, 732, 638, 26, 833, 879, 86, 563, 96]

arr = QuickSort(to_sort)
arr.sort()
# printouts after each step in each partition
[86, 787, 118, 555, 509, 130, 74, 444, 583, 632, 891, 598, 732, 638, 26, 833, 879, 630, 563, 96]
[86, 26, 118, 555, 509, 130, 74, 444, 583, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 96]
[86, 26, 74, 555, 509, 130, 118, 444, 583, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 96]
[86, 26, 74, 96, 509, 130, 118, 444, 583, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 555]
[26, 86, 74, 96, 509, 130, 118, 444, 583, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 555]
[26, 74, 86, 96, 509, 130, 118, 444, 583, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 555]
[26, 74, 86, 96, 509, 130, 118, 444, 555, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 583]
[26, 74, 86, 96, 118, 130, 509, 444, 555, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 583]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 632, 891, 598, 732, 638, 787, 833, 879, 630, 563, 583]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 891, 598, 732, 638, 787, 833, 879, 630, 632, 583]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 732, 638, 787, 833, 879, 630, 632, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 732, 638, 787, 833, 879, 630, 632, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 638, 787, 833, 879, 732, 632, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 632, 787, 833, 879, 732, 638, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 632, 638, 833, 879, 732, 787, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 632, 638, 732, 879, 833, 787, 891]
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 632, 638, 732, 787, 833, 879, 891]

# final output
[26, 74, 86, 96, 118, 130, 444, 509, 555, 563, 583, 598, 630, 632, 638, 732, 787, 833, 879, 891]
```


#### Time Complexity
We notice that each partition part does a scan over the subarray, which exhibits time complexity of `n`; the recursive quick_sort part is dividing the array into two parts each time, and hence like merge sort, _on average_, the depth of the recursion is expected to be of order `log(n)`. TOgether, we get the average and best case scenario of `nlog(n)`, since even in the best case, the scans and the partitions would still need to happen, and the fastest way to recursively sub-divide the original array down to empty or singleton arrays still requires `log(n)` time.

On the other hand, worst case could be quite dismal. The particular worst case would depend on the pivot selection strategy. For example, in our case, where we always choose the last element, worst case could happen if the array is already sorted (surprise?) or in complete reverse order. In these cases, the returned `pivot_ind` from the partition method would always be either the start or the end index, which essentially reduces 
```python
def quick_sort(self, start, end):
    """
    performs quick sort on the subarray
    """
    # only do so if we have something to sort
    if start < end:
        # get new pivot point
        pivot_ind = self.partition(start, end)

        # perform the quick_sort on the two new sub arrays
        self.quick_sort(start, pivot_ind-1)
        self.quick_sort(pivot_ind+1, end)
```
into something like:
```python
def quick_sort(self, start, end):
    """
    performs quick sort on the subarray - worst case
    """
    for i in range(len(self.arr)):
        self.partition(start, end-1)
```
Which makes the outer loop linear time, and the entire algorithm O(n<sup>2</sup>). Some variations of Quick Sort attempt to solve these issues, such as picking the pivot point randomly, or picking the median of the subarray, etc. Other improvements are of the same flavor as we have seen in Tim Sort's adaptation of Merge Sort; for example, Intro Sort (introspective sort) would pick and use specific sorting algorithm based on some initial _introspection_, and if it is determined that Quick Sort would be a suitable candidate for the particular array, it would only then be applied.


---


### Heap Sort


| ![heap-sort.gif](/assets/gifs/sorts/quick-sort.gif) |
|:--:|
| *(gif <a href="https://commons.wikimedia.org/wiki/File:Heap_sort_example.gif">Credit</a>)* |


To understand heap sort, let us first familiarize ourselves with a few definitions, which we will grab from Wikipedia:
1. **Binary tree**: A binary tree is a tree data structure in which each node has at most two children, which are referred to as the left child and the right child.
2. **Complete Binary Tree**: In a complete binary tree every level, except possibly the last, is completely filled, and all nodes in the last level are as far left as possible. It can have between 1 and 2h nodes at the last level h.
3. Data structure **Heap**: A heap is a specialized tree-based data structure which is essentially an almost complete tree that satisfies the heap property.
4. Heap Property: In a **max heap**, for any given node C, if P is a parent node of C, then the key (the value) of P is greater than or equal to the key of C. In a min heap, the key of P is less than or equal to the key of C. The node at the "top" of the heap (with no parents) is called the root node.
5. Array based representation of heaps: For a zero-based array, the root node is stored at index 0; if i is the index of the current node, then
```
  iParent(i)     = floor((i-1) / 2)
  iLeftChild(i)  = 2*i + 1
  iRightChild(i) = 2*i + 2
```

Now equipped with these concepts, we are ready to explore the actual Heap Sort. The way heap sort works is as follows:
1. First build out a max heap from the array;
2. At this point, the first element in the array representation of the heap, which is the root node of the heap, is the maximum element in the array;
3. Swap this element with the element at the end of the array, so that now the largest element is placed at the correct location;
4. Build a max heap from the remaining sub-array (excluding the last element);
5. Repeat 3 & 4 until the remaining sub-array is a single element.

Note that:
1. Step 1 and 4 are not identical. To build an arbitrary array into a max heap, we would need to start from the buttom up, since a standard heapify procedure can only be applied to nodes whose children nodes are all heapified. When we are on step 4, we only need to apply the heapify operation on the root node, since all other nodes are already heapified by construction.
2. This implementation sorts the original array in-place.

A more visual representation is provided below:

| ![heap-sort2.gif](/assets/gifs/sorts/heap-sort2.gif) |
|:--:|
| *(gif <a href="https://en.wikipedia.org/wiki/File:Sorting_heapsort_anim.gif">Credit</a>)* |


#### Python Implementation

```python
# heap sort
import math

class HeapSort():
    """
    Implements heap sorting.
    """
    def __init__(self, arr):
        self.arr = arr
        # keep track of effective heap length;
        # for example, after step 3 above,
        # heap_len would decrease by 1
        self.heap_len = len(self.arr)

    def build_max_heap(self):
        """
        Builds a max heap from a given, arbitrary array
        """
        # iParent(l) to find the last parent node in our heap
        i = math.floor((self.heap_len - 1) / 2)
        # will then use this as the starting point,
        # to build the max heap from the bottom up
        while i >= 0:
            # calls heapify on each i until the root node i == 0
            self.heapify(i)
            i -= 1

    def heapify(self, i):
        """
        heapify an array starting from the given index i,
        and up to self.heap_len
        After the run, nodes starting from i should
        be heapified.
        """

        # keep sifting down until we reach leaf of the heap
        while i < self.heap_len:
            # using the array representation, we have
            # the 2 children node index of i
            left = 2 * i + 1
            right = 2 * i + 2

            # If left index is >= length
            # then it doesn't exist (index 0-based)
            if left >= self.heap_len:
                break

            # Find the larger of the two children nodes
            larger_child_index = left
            if right < self.heap_len and self.arr[left] < self.arr[right]:
                larger_child_index = right

            # Check if node i itself is
            # smaller then left and right value.
            # If so, swap with the larger child to maintain heap.
            if self.arr[i] < self.arr[larger_child_index]:
                self.arr[i], self.arr[larger_child_index] = self.arr[larger_child_index], self.arr[i]

                # move on to the next node
                i = larger_child_index
            else:

                # Equal or larger than both children node, and done with the heapify
                break

    def pop_max_node(self):
        """
        Swap root node (max element)
        with the last index in current heap.

        Then perform procedures to maintain heap structure.
        """
        # swap root element
        self.arr[0], self.arr[self.heap_len - 1] =\
            self.arr[self.heap_len - 1], self.arr[0]
        # reduce heap length by 1
        self.heap_len -= 1
        # heapify resulting heap
        self.heapify(0)

    def sort(self):
        # first build max heap
        self.build_max_heap()
        # then repeat pop_max_node --> heapify procedure
        # until we are down to last element
        while self.heap_len > 1:
            self.pop_max_node()

        # done
        return self.arr
```

To test the code:

```python
# generate some random integers
import random
to_sort = random.sample(range(1, 1000), 20)

to_sort
[200, 390, 173, 316, 945, 83, 39, 740, 970, 132, 784, 412, 407, 221, 622, 11, 258, 560, 958, 109]

# initialize and run
arr = HeapSort(to_sort)
arr.sort()

# final output
[11, 39, 83, 109, 132, 173, 200, 221, 258, 316, 390, 407, 412, 560, 622, 740, 784, 945, 958, 970]
```


#### Time Complexity
- Heap Sort has `nlog(n)` time complexity for all cases.
- The height, or depth, of the heap containing n elements is of order `log(n)`. 
- There are two stages of the sorting operation, ran **one after another**, which means that the time complexity of the two parts are not multiplicative, but rather would be determined by the one with the higher order.
- `heapify` runs from i to `heap_len`, which is a maximum of `log(n)` complexity.
-  `build_max_heap` stage, we ran `heapify` starting from `math.floor((self.heap_len - 1) / 2)` down to root node, which makes it `n/2 * log(n) ~ nlog(n)` complexity.
- The second part of the sorting is running `pop_max_node` until `heap_len` is down to 1. Each `pop_max_node` is essentially 1 run of `heapify`, and with the outer loop running at order `n`, this part is also of order `nlog(n)`.
- Two parts being additative, the overall complexity is also `nlog(n)`.