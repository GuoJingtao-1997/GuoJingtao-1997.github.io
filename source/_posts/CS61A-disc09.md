---
author: 小郭
title: CS61A Disc09 - Linked Lists & Trees
cover: false
toc: true
mathjax: false
summary: >-
  断断续续做了一段时间才做到`Disc09`， 感觉这门课程还是比想象中的难些，个人理解前面主要就是学习了两种函数结构`higher-order
  functions`和`recursion`,两种数据结构`tree`和`linked-list`，可变类型`dict`和`list`以及`nonlocal`,`iterator`和`generator`的操作，并学习利用`environment
  diagram`分析`lambda`表达式和函数的调用，最后学习了`OOP`以及类的继承操作。
top: true
categories: Python
tags:
  - Disc09
  - CS61A Summer20
  - SICP for Python
abbrlink: 4fd3fcd6
date: 2021-01-06 00:00:00
img:
coverImg:
password:
---
# CS61A Summer20 Disc09

>我已经将目前完成的作业代码放到 `github` 上， 有兴趣的小伙伴可以去 `fork` 一份源码到你的仓库并 `clone` 到本地， <<<<<[源代码下载](https://github.com/GuoJingtao-1997/CS61A-SU2020)>>>>>

- 点击 [访问](https://inst.eecs.berkeley.edu/~cs61a/su20/) 直达这门课程的网站，
- `B站` 上有对应网课视频，点击 [观看](https://www.bilibili.com/video/BV1nJ41157p6)


---

## Linked Lists

### Implementation

A linked list is either an empty linked list, or a Link object containing a `first` value and the `rest` of the linked list.

```python
class Link:
    empty = ()
    def __init__(self, first, rest=empty):
        assert rest is Link.empty or isinstance(rest, Link)
        self.first = first
        self.rest = rest

    def __repr__(self):
    """Mainly used for debugging and development"""
        if self.rest:
            rest_str = "," + repr(self.rest)
        else:
            rest_str = " "
        return 'Link{0}{1}'.format(repr(self.first), rest_str)

    def __str__(self):
    """Used to describe the object to the end user"""
        string = '<'
        while self.rest != Link.empty:
            string += str(self.first) + ' '
            self = self.rest
        return string + str(self.first) + '>'
```

### Q1: Sum Nums

Write a function `sum_nums` that takes in a linked list and returns the sum of all its elements. You may assume all elements in `lnk` are integers.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution (Iteration)</font>

```python
def sum_nums(lnk):
    """
    >>> a = Link(1, Link(6, Link(7)))
    >>> sum_nums(a)
    14
    """
    nums = 0
    while lnk is not Link.empty:
        nums += lnk.first
        lnk = lnk.rest
    return nums
```

<font face="Times New Roman" color=red>PDF Solution (Recursion)</font>

```python
def sum_nums(lnk):
    """
    >>> a = Link(1, Link(6, Link(7)))
    >>> sum_nums(a)
    14
    """
    if lnk == Link.empty:
        return 0
    return lnk.first + sum_nums(lnk.rest)
```

### Q2: Multiply Links

Write a function that takes in a `Python list of linked lists` and multiplies them element-wise. It should return a <font face="Times New Roman" color=yellow style="font-weight:bold">new linked list</font>.

If not all of the Link objects are of equal length, return a linked list whose length is
that of the shortest <font face="Times New Roman" color=yellow style="font-weight:bold">shortest</font> linked list given. You may assume the Link objects are shallow linked lists, and that lst of lnks contains at least one linked list.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution (Recursion)</font>

```python
def multiply_lnks(lis_of_lnks):
    """
    >>> a = Link(2, Link(3, Link(5)))
    >>> b = Link(6, Link(4, Link(2)))
    >>> c = Link(4, Link(1, Link(0, Link(2))))
    >>> p = multiply_lnks([a, b, c])
    >>> p.first
    48
    >>> p.rest.first
    12
    >>> p.rest.rest.rest is Link.empty
    True
    """
    mul = 1
    for i in range(len(lis_of_lnks)):
        if lis_of_lnks[i] is Link.empty:
            return Link.empty
        mul *= lis_of_lnks[i].first
        lis_of_lnks[i] = lis_of_lnks[i].rest
    return Link(mul, multiply_lnks(lis_of_lnks))
```

<font face="Times New Roman" color=red style="font-weight:bold">PDF Solution (Recursion)</font>

```python
def multiply_lnks(lis_of_lnks):
    """
    >>> a = Link(2, Link(3, Link(5)))
    >>> b = Link(6, Link(4, Link(2)))
    >>> c = Link(4, Link(1, Link(0, Link(2))))
    >>> p = multiply_lnks([a, b, c])
    >>> p.first
    48
    >>> p.rest.first
    12
    >>> p.rest.rest.rest is Link.empty
    True
    """
    product = 1
    for lnk in lst_of_lnks:
        if lnk is Link.empty:
            return Link.empty
        product *= lnk.first
    lst_of_lnks_rests = [lnk.rest for lnk in lst_of_lnks] 
    return Link(product, multiply_lnks(lst_of_lnks_rests))
```

The PDF recursive solution shown above seems to be similar to mine.

<font face="Times New Roman" color=red style="font-weight:bold">PDF Solution (Iteration)</font>

```python
def multiply_lnks(lis_of_lnks):
    """
    >>> a = Link(2, Link(3, Link(5)))
    >>> b = Link(6, Link(4, Link(2)))
    >>> c = Link(4, Link(1, Link(0, Link(2))))
    >>> p = multiply_lnks([a, b, c])
    >>> p.first
    48
    >>> p.rest.first
    12
    >>> p.rest.rest.rest is Link.empty
    True
    """
    import operator
    from functools import reduce 
    def prod(factors): 
        return reduce(operator.mul, factors, 1)
    
    head = Link.empty
    tail = head
    while Link.empty not in lst_of_lnks:
        all_prod = prod([l.first for l in lst_of_lnks])
        if head is Link.empty:
            head = Link(all_prod) 
            tail = head
        else:
            tail.rest = Link(all_prod) 
            tail = tail.rest
        lst_of_lnks = [l.rest for l in lst_of_lnks]
    return head
```

>`reduce()` is a function that apply function of two arguments cumulatively to the items of iterable, from left to right, so as to reduce the iterable to a single value. The syntax is <font face="Times New Roman" color=yellow style="font-weight:bold">reduce(function, iterable[, initializer])</font>

### Q3: Filter Link

Implement `filter_link`, which takes in a linked list `link` and a function `f` and
returns a generator which yields the values of link for which f returns `True`.

Try to implement this both using a while loop and without using any form of
iteration !

<font face="Times New Roman" color=red style="font-weight:bold">My Solution (Generator)</font>

```python
def filter_link(link, f):
    """
    >>> link = Link(1, Link(2, Link(3)))
    >>> g = filter_link(link, lambda x: x % 2 == 0)
    >>> next(g)
    2
    >>> next(g)
    StopIteration
    >>> list(filter_link(link, lambda x: x % 2 != 0))
    [1, 3]
    """
    while link != Link.empty:
        try:
            if f(link.first):
                yield link.first
            link = link.rest
        except StopIteration:
            print("StopIteration")
```

<font face="Times New Roman" color=red style="font-weight:bold">My Solution (Recursion)</font>

```python
def filter_no_iter(link, f):
    """
    >>> link = Link(1, Link(2, Link(3)))
    >>> list(filter_link(link, lambda x: x % 2 != 0))
    [1, 3]
    """ 
    if link is Link.empty:
        return 
    elif f(link.first):
        return [link.first] + filter_no_iter(link.rest,f)
```

## Trees

### Implementation

A tree is defined as having a `label` and some
`branches`. Previously, we implemented the tree abstraction using Python lists. Let’s
look at another implementation using objects instead:

```python
class Tree:
    def __init__(self, label, branches=[]):
        for b in branches:
            assert isinstance(b, Tree)
        self.label = label
        self.branches = list(branches)

    def is_leaf(self):
        return not self.branches

    def __repr__(self):
    """Mainly used for debugging and development"""
        if self.branches:
            tree_str = ", " + repr(self.branches)
        else:
            tree_str = ""
        return  "Tree({0}{1})".format(self.label, tree_str)

    
    def __str__(self):
    """Used to describe the object to the end user"""
        def print_tree(t, indent=0):
            tree_str = '  ' * indent + str(t.label) + "\n"
            for b in t.branches:
                tree_str += print_tree(b, indent + 1)
            return tree_str
        return print_tree(self).rstrip()
```

### Q4: Make Even

Define a function `make_even` which takes in a tree `t` whose values are integers, and
mutates the tree such that all the odd integers are increased by 1 and all the even
integers remain the same.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution</font>

```python
def make_even(t):
    """
    >>> t = Tree(1, [Tree(5, [Tree(3)]), Tree(4), Tree(5)])
    >>> make_even(t)
    >>> t.label
    2
    >>> t.branches[0].branches[0].label
    4
    >>> t.branches[0].label
    6
    """
    if t.is_leaf():
        return
    elif t.label % 2 != 0:
        t.label += 1
    for b in t.branches:
        if b.label % 2 != 0:
            b.label += 1
        make_even(b)
```

<font face="Times New Roman" color=red style="font-weight:bold">PDF Solution</font>

```python
def make_even(t):
    """
    >>> t = Tree(1, [Tree(5, [Tree(3)]), Tree(4), Tree(5)])
    >>> make_even(t)
    >>> t.label
    2
    >>> t.branches[0].branches[0].label
    4
    >>> t.branches[0].label
    6
    """
    if t.label % 2 != 0:
        t.label += 1
    for b in t.branches:
        make_even(b)
```

### Q5: Square Tree

Define a function `square_tree(t)` that squares every value in the non-empty tree
`t`. You can assume that every value is a number.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution</font>

```python
def square_tree(t):
    """
    Mutates a Tree t by squaring all its elements.

    >>> t = Tree(2, [Tree(5, [Tree(3)]), Tree(4), Tree(5)])
    >>> square_tree(t)
    >>> t.label
    4
    >>> t.branches[0].branches[0].label
    9
    >>> t.branches[0].label
    25
    """
    t.label **= 2
    for b in t.branches:
        square_tree(b)
```

### Q6: Find Path

Define the procedure `find_path` that, given a Tree `t` and an `entry`, returns a list containing the nodes along the path required to get from the `root of t` to entry. If entry is not present in t, return `False`.

Assume that the elements in `t` are unique. Find the path to an element.

![Tree diagram for tree_ex instance](https://cdn.jsdelivr.net/gh/GuoJingtao-1997/PicBed/BlogImg/Tree0.png)

<font face="Times New Roman" color=red style="font-weight:bold">Solution for Returning the First Possible Path</font>

```python
def find_path(t, entry):
    """
    Return False if entry is not present in t, 
    else return a path from the root of t to entry

    >>> tree_ex = Tree(2, [Tree(7, [Tree(3), Tree(6, [Tree(5), Tree(11)])]), Tree(5)])
    >>> find_path(tree_ex, 5)
    [2, 7, 6, 5]
    >>> find_path(tree_ex, 12)
    False
    """
    if t.label == entry:
        return [entry]
    for b in t.branches:
        path = find_path(b, entry)
        if path:
            return [t.label] + path
    return False
```

<font face="Times New Roman" color=red style="font-weight:bold">Solution for Returning All Possible Paths</font>

```python
def find_path(t, entry):
    """
    Return False if entry is not present in t, 
    else return a path from the root of t to entry

    >>> tree_ex = Tree(2, [Tree(7, [Tree(3), Tree(6, [Tree(5), Tree(11)])]), Tree(5)])
    >>> find_path(tree_ex, 5)
    [[2, 7, 6, 5], [2, 5]]
    >>> find_path(tree_ex, 12)
    False
    """
    def helper(t, entry):
        paths = []
        if t.label == entry:
            paths.append([entry])
        for b in t.branches:
            for path in helper(b, entry):
                paths.append([t.label] + path)
        return paths
    paths = helper(t, entry)
    return paths if paths else False
```

### Q7: Average

Assuming that every value in `t` is a number, define `average(t)`, which returns the average of all the values in t. You may not need to use all the provided lines.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution</font>

```python
def average(t):
    """
    Return the average value of all the nodes in t.

    >>> t0 = Tree(0, [Tree(1), Tree(2, [Tree(3)])])
    >>> average(t0)
    1.5
    >>> t1 = Tree(8, [t0, Tree(4)])
    >>> average(t1)
    3.0
    """
    def sum_helper(t):
        total, count = t.label, 1
        for b in t.branches:
            total_b, count_b  = sum_helper(b)
            total, count = total + total_b, count + count_b
        return total, count
    total, count = sum_helper(t)
    return total / count
```

### Q8: Combine Tree

Write a function that combines the values of two trees `t1` and `t2` together with the `combiner` function. Assume that t1 and t2 have identical structure. This function should return a <font face="Times New Roman" color=yellow style="font-weight:bold">new</font> tree

Hint: consider using the `zip()` function, which returns an iterator of tuples where
the first items of each iterable object passed in form the first tuple, the second items
are paired together and form the second tuple, and so on and so forth.

<font face="Times New Roman" color=red style="font-weight:bold">My Solution</font>

```python
def combine_tree(t1, t2, combiner):
    """
    Combines the values of two trees t1 and t2 together with the combiner function
    This function should return a new tree.
    
    >>> a = Tree(1, [Tree(2, [Tree(3)])])
    >>> b = Tree(4, [Tree(5, [Tree(6)])])
    >>> combined = combine_tree(a, b, mul)
    >>> combined.label
    4
    >>> combined.branches[0].label
    10
    """
    t = Tree(combiner(t1.label, t2.label))
    for b in zip(t1.branches, t2.branches):
        t = Tree(combiner(t1.label, t2.label), [combine_tree(b[0], b[1], combiner)])
    return t
```

<font face="Times New Roman" color=red style="font-weight:bold">PDF Solution Using List Comprehension</font>

```python
def combine_tree(t1, t2, combiner):
    """
    Combines the values of two trees t1 and t2 together with the combiner function
    This function should return a new tree.
    
    >>> a = Tree(1, [Tree(2, [Tree(3)])])
    >>> b = Tree(4, [Tree(5, [Tree(6)])])
    >>> combined = combine_tree(a, b, mul)
    >>> combined.label
    4
    >>> combined.branches[0].label
    10
    """
    branch = [combine_tree(b[0], b[1], combiner) for b in zip(t1.branches, t2.branches)]
    return Tree(combiner(t1.label, t2.label), branch)
```

### Q9: Alternate Tree Map

Implement the `alt_tree_map` function that, given a `function` and a `Tree`, applies the `function` to all of the data
at every other level of the tree,starting at the root

<font face="Times New Roman" color=red style="font-weight:bold">My Solution Using Help Function</font>

```python
def alt_tree_map(t, map_fn):
    """
    >>> t = Tree(1, [Tree(2, [Tree(3)]), Tree(4)])
    >>> negate = lambda x: -x
    >>> alt_tree_map(t, negate)
    Tree(-1, [Tree(2, [Tree(-3)]), Tree(4)])
    """
    def map_helper(t, depth=0):
        if depth % 2 == 0:
            t.label = map_fn(t.label)
        for b in t.branches:
            map_helper(b, depth + 1)
        return t
    return map_helper(t)
```

<font face="Times New Roman" color=red style="font-weight:bold">PDF Solution With Returning a New Tree and Using List Comprehension</font>

```python
def alt_tree_map(t, map_fn):
    """
    >>> t = Tree(1, [Tree(2, [Tree(3)]), Tree(4)])
    >>> negate = lambda x: -x
    >>> alt_tree_map(t, negate)
    Tree(-1, [Tree(2, [Tree(-3)]), Tree(4)])
    """
    label, branch = map_fn(t.label), []
    for b in t.branches:
        b0 = [alt_tree_map(bb, map_fn) for bb in b.branches]
        branch.append(Tree(b.label, b0))
    return Tree(label, branch)
```












