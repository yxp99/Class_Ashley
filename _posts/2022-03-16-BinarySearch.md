---
layout: post
title: Binary Search
date: 2022-03-16
author: Shadow Walker
tags: [上课记录, BinarySearch]
toc: true
comments: true
---

## BinarySearch

两种方式

### left<=right

```java
// 这个的优势是思路更清晰, 但是当无解的时候会存在 out of bound 的问题. 
private int binarySearch(int left, int right)
{
    while(left <= right)
    {
        int pivot = (left + right )/2;
        if(nums[pivot] == target){
            return pivot;
        }else if(nums[pivot]<target){
            left = pivot +1;
        }else{
            right = pivot -1;
        }
    }
    return -1;
}
```

### left<right


```java
// 这个优势是不会存在bug, 但是思维上不好记. 
while (left < right) {
	  // 这样取值的优势是防止 right or left = Integer.MAX_VALUE, 这样可以防止integer overflow. 
     int m = left + (right - left)/2  
     if(nums[m] < target) {
    	Left = m + 1  
    } else {
	Right = m 
   }
}
```

Binary的难点在于边界判断, 对于边界判断, 就刷1095题, 一道题基本涵盖了所以可能的binary search边界处理. 对于理解bianry和divide&conquer都大有帮助. 

## Search in sorted array/matrix

### Basic Simple Applications

- Leetcode: 34, 35, 278.  简单搜索题目, 如果对模板不熟, 可以拿来刷.  


### 74. Search a 2D Matrix

```java
class Solution {
    public boolean searchMatrix(int[][] matrix, int target) {
        int ROW = matrix.length;
        int COL = matrix[0].length;
        
        if(ROW == 0 || COL == 0) return false;
        
        // binary search column, then binary search row. 
        int low = 0;
        int high = ROW*COL - 1;
        
        while(low <= high){
            int index = (low + high)/2;
            //System.out.println(low + " " + high + " " + index);
            int pick = matrix[index/COL][index%COL];
            if(pick == target){
                return true;
            }else if(pick < target){
                low = index + 1;
            }else {
                high = index - 1;
            }
        }
        
        return false;
    }
}
```

## 查 peak 类问题


## 区间取值问题

这类问题的特点是根据条件可以选取一个最大值和最小值, 然后在这两个点之间选取满足题目条件的值.  满足条件的值肯定是min/max. 

- 这类问题给定的array一定是不含负数的, 不然判断不能用O(n)达成
- 在subarray上的取值一般是有连续性的, 但也有例外(1482题), 但即使不是连续取值, 但至少for loop的比较过程也一定是O(n). 


### 1011. Capacity To Ship Packages Within D Days

```java 
class Solution {
    // We binary search the final result.
    // The left bound is max(A), the min capacity can be no less than the max weight. 
    // The right bound is sum(A). the max capacity can be no more than total sum. 
    public int shipWithinDays(int[] weights, int days) {
        int left = 0, right = 0;
        // build our left and right boundary. 
        for(int weight: weights){
            right += weight;
            left = Math.max(left, weight);
        }
        while(left<right){
            int mid = (left+right)/2, need = 1, curr = 0;
            for(int weight: weights){
                // This for loop is used to test if the the capcity we are at is valid. 
                curr += weight;
                if(curr>mid){
                    curr = weight;
                    need ++;
                }
            }
            if(need>days) 
                // if what is needed for the current capacity is more than days, then we increase capacity. 
                left = mid+1;
            else
                // else, decrease the right boundary, get a lower capacity. 
                right = mid;
        }
        return left;
    }
}
```


### 875. Koko Eating Bananas

跟1011题是一样的, 只不过换个思路, left 和 right的取值根据题的要求有不同而已.  标准答案的left, right取值不好, 太笨拙, 看我的. 

```java
class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int left = 0, right = 0, sum = 0;
        for(int pile: piles){
            right = Math.max(right, pile);
            sum += pile;
        }
        left = sum/h;
        if(left == 0)
            left = 1;
        if(h<=piles.length){
            return right;
        }
        while(left<right){
            int curr = 0, need = 0, mid = left + (right - left)/2;
            for(int pile: piles){
                if(need>h)
                    break;
                need += pile/mid;
                if(pile%mid!=0)
                    need ++;
            }
            if(need>h)
                left = mid+1;
            else
                right = mid;
        }
        return left;
    }
}
```


### 1482. Minimum Number of Days to Make m Bouquets

思路还是跟1011一样, 这种题一旦知道是binary search之后就没啥难度了. 关键就在于如何去想是用binary search

```java
class Solution {
    public int minDays(int[] bloomDay, int m, int k) {
        if(m*k>bloomDay.length)
            return -1;
        int left = Integer.MAX_VALUE, right = 0;
        for(int day: bloomDay){
            right = Math.max(day, right);
            left = Math.min(day, left);
        }
        while(left<right){
            int middle = left + (right-left)/2, curr = 0, bouNum = 0;
            for(int day: bloomDay){
                if(bouNum>=m)
                    break;
                if(day<=middle){
                    curr ++;
                    if(curr==k){
                        curr = 0; 
                        bouNum ++;
                    }
                }
                else
                    curr = 0;
            }
            if(bouNum>=m)
                right = middle;
            else
                left = middle + 1;
                
        }
        return left;
    }
}
```

### 1231. Divide Chocolate

Maximize Smallest Sum. 

一开始想了半天, 还是没想明白为什么是curr = 0.  直到看到评论区

> Since we are looking for minimum now, it is okay if subarray sum greater. In no. 410, if the sum of subarray section greater than target is forbidden, since the target is our maximum sum of subarray. However, in this problem, we look for the lower bound and make sure the number of splits is correct. Thus, when total > target we treat the new element in the same subarray so we reset total = 0. On the other hand, we need separate the new element in another subarray so we set total = num. 

```java
class Solution {
    public int maximizeSweetness(int[] sweetness, int k) {
        // set our left and right boundary
        int left = Integer.MAX_VALUE, right = 0;
        for(int sweet: sweetness){
            left = Math.min(left, sweet);
            right += sweet;
        }
        // Two edge cases
        if(k==0)
            return right;
        if(sweetness.length<=k+1)
            return left;
        right = right/(k+1);
        
        // binary search and find the result. 
        while(left<=right){
            int middle = left + (right-left)/2, curr = 0, piece = 1;
            for(int sweet: sweetness){
                curr += sweet;
                if(curr > middle){
                // 这里curr = 0 是这道题的难点. 需要反复思考. 
                    curr = 0;
                    piece ++;
                }
            }
            if(piece > k+1)
                left = middle + 1;
            else
                right = middle - 1;
        }
        return left;
    }
}
```

```java
class Solution {
    public int maximizeSweetness(int[] sweetness, int k) {
        // set our left and right boundary
        int left = Integer.MAX_VALUE, right = 0;
        for(int sweet: sweetness){
            left = Math.min(left, sweet);
            right += sweet;
        }
        right = right/(k+1);
        
        // Two edge cases
        if(k==0)
            return right;
        if(sweetness.length<=k+1)
            return left;
        
        
        // binary search and find the result. 
        while(left<right){
            int middle = left + (right-left)/2, curr = 0, piece = 1;
            for(int sweet: sweetness){
                curr += sweet;
                if(curr > middle){
                    curr = 0;
                    piece ++;
                }
            }
            if(piece > k+1)
                left = middle + 1;
            else
                right = middle;
        }
        return left;
    }
}
```

### 410. Split Array Largest Sum

Minimize Largest Sum. 

**拆分方法用来模板**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int left = 0, right = 0;
        for(int num: nums){
            left = Math.max(left, num);
            right += num;
        }
        
        while(left < right){
            int mid = left + (right - left) / 2;
            if(count(mid, nums) > m){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        
        return right;
    }
    
    private int count(int target, int[] nums){
        int sum = 0, count = 0;
        for(int num : nums){
            if( sum + num > target){
                count += 1;
                sum = num;
            }else{
                sum += num;
            }
        }
        return count + 1;
    }
}
```

**left<=right**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int left = 0, right = 0;
        for(int num: nums){
            left = Math.max(left, num);
            right += num;
        }
        
        if(m==1)
            return right;
        
        while(left<=right){
            int middle = left + (right-left)/2, curr = 0, count = 1;
            // count is 1, because in the for loop below, the last subarray is not counted. 
            for(int num: nums){
                curr += num;
                if(curr>middle){
                    curr = num;
                    count ++;
                }
            }
            if(count > m)
                left = middle + 1;
            else
                right = middle - 1;
        }
        return left;
    }
}
```

**left<right**

```java
class Solution {
    public int splitArray(int[] nums, int m) {
        int left = 0, right = 0;
        for(int num: nums){
            left = Math.max(left, num);
            right += num;
        }
        
        if(m==1)
            return right;
        
        while(left<right){
            int middle = left + (right-left)/2, curr = 0, count = 1;
            // count is 1, because in the for loop below, the last subarray is not counted. 
            for(int num: nums){
                curr += num;
                if(curr>middle){
                    curr = num;
                    count ++;
                }
            }
            if(count > m)
                left = middle + 1;
            else
                right = middle;
        }
        return left;
    }
}
```

### 774. Minimize Max Distance to Gas Station

774 可以算作这一个系列里面最难的了. 

```java
class Solution {
    public double minmaxGasDist(int[] stations, int K) {
        double left = 0, right = stations[stations.length - 1] - stations[0];

        while (left +1e-6 < right) {
            double mid = left + (right - left) / 2;
            int count = 0;
            for (int i = 0; i < stations.length - 1; ++i)
                count += (int)((stations[i + 1] - stations[i]) / mid);
            if (count > K) 
                left = mid;
            else 
                right = mid;
        }
        return right;
    }
}
```

## 非常规

### 540. Single Element in a Sorted Array

非常规的一道题.  代码一看就懂, 想出这样可以搜出来倒是不难, 难的是边界问题讨论. 这道题无法直接转化成 low<=high, 怎么写, 我还没想出来. 

```java
class Solution {
    public int singleNonDuplicate(int[] nums) {
        int low = 0, high = nums.length - 1;
        while(low<high){
            int mid = low + (high-low)/2;
            if(mid%2==1) mid--;
            if(nums[mid] == nums[mid+1])
                low = mid + 2;
            else
                high = mid;
        }
        return nums[low];
    }
}
```

