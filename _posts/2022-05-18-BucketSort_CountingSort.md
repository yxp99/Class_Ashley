---
layout: post
title: Bucket Sort & Counting Sort
date: 2022-05-18
author: Shadow Walker
tags: [Sorting]
toc: true
comments: true
---


## Overview

- easy: Bubble, Insertion, Selection, 
- core: Merge, Quick, Heap. 
- rare: Bucket, Counting, Radix. 

## Practice

- [912 Sort an Array](https://leetcode.com/problems/sort-an-array/) 这道题可以用来练习所有的sorting

## Bucket Sort

### Algorithm

- [whole Algorithm with diagram](https://iq.opengenus.org/time-and-space-complexity-of-bucket-sort/). 
![](https://media.geeksforgeeks.org/wp-content/cdn-uploads/scene01801.jpg)

```
define function bucketSort(arr[]) as
    step 1: create as many empty buckets (lists or arrays) as the length of the input array
    step 2: store the array elements into the buckets based on their values (described in step 3)
    step 3: for i from 0 to length of arr do
                (a) bucket_index = int((n * arr[i]) / 10) //here n is the length of the input array
                (b) store the element arr[i] at bucket[bucket_index]
    step 4: for i from 0 to length of bucket do:
                (a) sort the elements stored inside bucket[i], either by applying recursion or by 
                    some other sorting method
    step 5: push the values stored in each bucket starting from lowest index to highest index to a 
            new array/list
step 6: return the sorted array/list
```

### When to use

- Bucket sort assumes that the input is drawn from a uniform distribution.
- The computational complexity estimates involve the number of buckets.
- Bucket sort can be exceptionally fast because of the way elements are assigned to buckets, typically using an array where the index is the value.
- This means that more auxiliary memory is required for the buckets at the cost of running time than more comparison sorts.


### Why inner sort is O(1)

Because we assume there are lots of bucket and each bucket only have a few elements. A few menas the size of the bucket is really small compare to the length of the array. 

### 347. Top K Frequent Elements


```java
class Solution {
    public int[] topKFrequent(int[] nums, int k) {

        List<Integer>[] bucket = new List[nums.length + 1];
        Map<Integer, Integer> frequencyMap = new HashMap<Integer, Integer>();

        for (int n : nums) {
            frequencyMap.put(n, frequencyMap.getOrDefault(n, 0) + 1);
        }

        for (int key : frequencyMap.keySet()) {
            int frequency = frequencyMap.get(key);
            if (bucket[frequency] == null) {
                bucket[frequency] = new ArrayList<>();
            }
            bucket[frequency].add(key);
        }

        List<Integer> res = new ArrayList<>();
        
        for (int pos = bucket.length - 1; pos >= 0 && res.size() < k; pos--) {
            if (bucket[pos] != null) {
                res.addAll(bucket[pos]);
            }
        }
        
        int[] output = new int[k];
        for(int i = 0; i < output.length; i ++){
            output[i] = res.get(i);
        }
        
        return output;
    }
}
```

### Time complexity & space complexity

- Time complexity: `O(n + k)`
- Space Complexity: `O(n + k)`
- Worst case: `O(n²)`
- Best Case: `O(n + k)`
- Average Case: `O(n + n²/k + k)`, `O(n)` when `k = Θ(n)`

where

- n is the number of elements
- k is the number of buckets

## Counting Sort

- non-comparison sort. The max time complexity of comparison sort cannot be less than O(nlogn)


Counting sort operates by counting the number of objects that have each distinct key value, and using arithmetic on those tallies to determine the positions of each key value in the output sequence. Its running time is linear in the number of items and the difference between the maximum and minimum keys, so **it is only suitable for direct use in situations where the variation in keys is not significantly greater than the number of items**.

### 两种实现方式

- 第一种, 目前找到[最好的图示理解](https://www.evernote.com/shard/s573/sh/2d2e97f4-cdf3-4e4a-acdf-b1e38d110709/76bef9329ebc7082c1060712a08845b2), 一看就懂
- 第二种, [Video](https://youtu.be/7zuGmKfUt7s)

### 912 Basic Sorting

- 普遍常规快速版版

这一版traverse 原 array 需要花时间理解, 优势是快. 

```java
class Solution {
    public int[] sortArray(int[] nums) {
        //-50000 <= nums[i] <= 50000
        // you can even use a smaller array if you know min and max values
        int[] count = new int[100_001]; 
        for (int num: nums) {
            count[num + 50_000]++;
        }
        
        for(int i = 1; i < 100_001; i ++){
            count[i] += count[i - 1];
        }
        
        int[] output = new int[nums.length];

        for(int i = nums.length - 1; i >= 0; i --){
            output[count[nums[i] + 50_000] - 1] = nums[i];
            -- count[nums[i] + 50_000];
        }

        return output;
    }
}
```

- 简洁版

这一版来自leetcode, 更易懂, traverse count array但慢. 

```java
class Solution {
    public int[] sortArray(int[] nums) {
        //-50000 <= nums[i] <= 50000
        // you can even use a smaller array if you know min and max values
        int[] count = new int[100_001]; 
        for (int c: nums) {
            count[c + 50_000]++;
        }
        // use new array
        int[] res = new int[nums.length];
        int i = 0;
        for (int j = 0; j < count.length; j++) {
            while (count[j] > 0) {
                res[i++] = j - 50_000;
                count[j]--;
            }
        }
        return res;
    }
}
```

### 274 H-Index

```java
public class Solution {
    public int hIndex(int[] citations) {
        int n = citations.length;
        int[] papers = new int[n + 1];
        // counting papers for each citation number
        for (int c: citations)
            papers[Math.min(n, c)]++;
        // finding the h-index
        int k = n;
        for (int s = papers[n]; k > s; s += papers[k])
            k--;
        return k;
    }
}
```

### 为什么倒着traverse

- [这样倒着遍历](https://www.jianshu.com/p/28b4b24fe28b)，而且放进去一个就将C数组对应的值（表示前面有多少元素小于或等于A[i]）减去一。如果有相同的数x1,x2，那么相对位置后面那个元素x2放在（比如下标为4的位置），相对位置前面那个元素x1下次进循环就会被放在x2前面的位置3（因为C[A[J]]--）。从而保证了稳定性。
- 如果不倒着会使相同元素的相对位置发生改变. 

### Time and space complexity

- Time Complexity: `O(n+k)` where n is the number of elements in the input array and k is the range of input. 
- Space Complexity: `O(n+k)`

## Problems

- [1094 Car Pooling](https://leetcode.com/problems/car-pooling/)
- [274 H-Index](https://leetcode.com/problems/h-index/)
- [164 Maximum Gap](https://leetcode.com/problems/maximum-gap/)


## Resource

- [Bucket Sort opengenus](https://iq.opengenus.org/time-and-space-complexity-of-bucket-sort/)