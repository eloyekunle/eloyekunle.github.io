---
layout: post
title: Haskell - Filter Positions In List
category: Blog
tags: [haskell, hackerrank]
---

**Github**: [FilterPositionsInList.hs](https://github.com/eloyekunle/haskell_snippets/blob/master/FilterPositionsInList.hs)

We will now manipulate Lists by filtering through and returning only the elements that occur in the odd positions.

We declare our function type which takes in a List as an argument and returns a List.


```haskell
type FilterPos = [Int] -> [Int]
```

### Using List Comprehension


```haskell
f :: FilterPos
f lst = [lst!!n | n<-[0..length lst - 1], odd n]
```


```haskell
f [2,4,6,8,10,12,14]
```


    [4,8,12]


### Using Recursion


```haskell
f2 :: FilterPos
f2 (_:x:xs) = x : f2 xs
f2 _ = []
```


```haskell
f2 [2,4,6,8,10,12,14]
```


    [4,8,12]


### Using Higher Order Functions (`map`, `filter`, `zip`)


```haskell
f3 :: FilterPos
f3 = map snd . filter (odd . fst) . zip [0..]
```


```haskell
f3 [2,4,6,8,10,12,14]
```


    [4,8,12]
