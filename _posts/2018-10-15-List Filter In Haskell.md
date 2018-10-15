---
layout: post
title: Haskell - List Filter in Haskell
category: Technology
tags:
    - haskell
    - hackerrank
---

**Github**: [ArrayFilter.hs](https://github.com/eloyekunle/haskell_snippets/blob/master/ArrayFilter.hs)  
We'll continue list manipulation by considering how to filter a list.  
Specifically, given a number `n` and an array, we'll return all elements of the array less than `n`.  

First we declare the `type` of our function, which takes a number and an array, and returns an array.


```haskell
type ArrFilter = Int -> [Int] -> [Int]
```

### Using Recursion


```haskell
f :: ArrFilter
f _ [] = []
f n (x:xs) =
    if x < n
        then x : f n xs
    else
        f n xs
```


```haskell
f 10 [2,4,6,8,10,12,14]
```


    [2,4,6,8]


### Using ListComp
List comprehensions might look a bit familiar if you've used them in Python before.


```haskell
f2 :: ArrFilter
f2 n arr = [num | num <- arr, num < n]
```


```haskell
f2 10 [2,4,6,8,10,12,14]
```


    [2,4,6,8]


### Using Filter


```haskell
f3 :: ArrFilter
f3 n arr = filter (< n) arr
```


```haskell
f3 10 [2,4,6,8,10,12,14]
```


    [2,4,6,8]


### Using Filter - Short


```haskell
f4 :: ArrFilter
f4 n = filter (< n)
```


```haskell
f4 10 [2,4,6,8,10,12,14]
```


    [2,4,6,8]


### Using Filter - Shorter


```haskell
f5 :: ArrFilter
f5 = filter . (>)
```


```haskell
f5 10 [2,4,6,8,10,12,14]
```


    [2,4,6,8]
