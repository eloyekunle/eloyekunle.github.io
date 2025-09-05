---
layout: post
title: Haskell - Repeat List Elements
category: Blog
tags: [haskell, hackerrank]
---

**Github**: [RepeatArrayElements.hs](https://github.com/eloyekunle/haskell_snippets/blob/master/RepeatArrayElements.hs)

We'll explore some ways to carry out some List operations in Hasell.  
Specifically, we'll write functions that repeat each element of a list a specific (`n`) number of times.

Our function signature. They take in an integer `n` and a List of integers, and return a list of integers.


```haskell
type RepElms = Int -> [Int] -> [Int]
```

### Doing it Bottom-Up


```haskell
rep :: Int -> Int -> [Int]
rep n x =
  if n == 1
    then [x]
    else x : rep (n-1) x
```

- Using Recursion


```haskell
f :: RepElms
f _ [] = []
f n (x:xs) =
  if null xs
    then rep n x
  else rep n x ++ f n xs
  
f 3 [2,4,6,8,10,12,14]
```


    [2,2,2,4,4,4,6,6,6,8,8,8,10,10,10,12,12,12,14,14,14]


- Using `replicate`


```haskell
f2 :: RepElms
f2 _ [] = []
f2 n (x:xs) =
  if null xs
    then replicate n x
  else rep n x ++ f2 n xs

f2 2 [1,5,9]
```


    [1,1,5,5,9,9]


### Using `concatMap` with `replicate`


```haskell
f3 :: RepElms
f3 n = concatMap (replicate n)

f3 4 [2,4]
```


    [2,2,2,2,4,4,4,4]



```haskell
f5 :: RepElms
f5 = concatMap . replicate

f5 2 [9, 10]
```


    [9,9,10,10]



```haskell
f6 :: RepElms
f6 n arr = arr >>= replicate n

f6 5 [5, 6]
```


    [5,5,5,5,5,6,6,6,6,6]


### Using Nested ListComps


```haskell
f4 :: RepElms
f4 n arr =
  [num | num <- arr, _ <- [1..n]]
  
f4 3 [7, 4]
```


    [7,7,7,4,4,4]
