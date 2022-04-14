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
while (left < right) {
	  // 这样取值的优势是防止 right or left = Integer.MAX_VALUE, 这样可以防止integer overflow. 
     int m = left + (right - left)/2  
     if(nums[m] < target) {   // 注意这里, 大部分题目给的都是符合条件的判断, 而并非给的是不符合条件, 所以其实写符合条件的判断, 更不容易出错. 
    	left = m + 1;
    } else {
	right = m;
   }
}
```

优势: 

- 结束的时候 left = right, 不存在讨论取 left 还是取 right 的问题
- 可以安心使用 nums[m + 1], 绝对不会超出边界. 

### lower bound & upper bound

- [Guide](https://www.evernote.com/shard/s573/sh/19cf5eb7-83a7-4193-8f9e-b59f729b2255/6088966f5ea5198ba0ebd13f8f8078bc) from shawynLyn. 注意这个guide里面的默认排序是从低到高, 如果是从高到低, 那么 lower 和 upper bound应该是正好反过来才对. 见peak类问题. 

upper bound 的表达方式有三种:

- int mid = left + (right - left) / 2 + 1
- int mid = right - (right - left) / 2
- int mid = left + (right - left + 1) / 2

这三种都是一样的, 我觉得第一种更make sense. 因为 lower bound的表达方式一般只有一种, 即 `int mid = left + (right - left) / 2`, 第一种可以与之完美对应. 

栗子: [34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/)


Binary的难点在于边界判断, 对于边界判断, 就刷1095题, 一道题基本涵盖了所以可能的binary search边界处理. 对于理解bianry search大有帮助. 


## Search in sorted array/matrix

### when to use

- sorted array/matrix. (must be for the first type)
- too simple to solve with O(n), for example : [LC 1060](https://leetcode.com/problems/missing-element-in-sorted-array/)

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

### 查 peak 类问题

- 162 Find Peak Element
- 852 Peak Index in a Mountain Array
- 1901 Find a Peak Element II
- 1095 Find in Mountain Array

### 非常规 

Binary search 有一些非常规题目比如 [540 Single Element in a Sorted Array ](https://leetcode.com/problems/single-element-in-a-sorted-array/).  代码一看就懂, 想出这样可以搜出来倒是不难, 难的是边界问题讨论. 这道题无法直接转化成 low<=high, 怎么写, 我还没想出来.  但虽然解法非常规, 依然是sorted, 不难判断出是 binary search

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


## 区间取值问题 | 切糕问题

### When to use

这类问题的特点是根据条件可以选取一个最大值和最小值, 然后在这两个点之间选取满足题目条件的值.  满足条件的值肯定是min/max. 

- 这类问题给定的array一定是不含负数的, 不然判断不能用O(n)达成
	- 2022-04-12: 这条今天想了一下其实不确定, 但目前没有看到用过负数的例子. 
- 在array上的取值一般是有连续性的, 目前还没有看到例外, 1482题比较特殊, 虽然用了条件判断, 但也是连续的. 
- 无论求 min/max, 必须是用在每一步的判断, 不存在叠加关系, 不然如果是整体的min/max, 则需要用DP, 例如 [Leetcode 1000](https://leetcode.com/problems/minimum-cost-to-merge-stones/)
- for loop的比较过程也一定是O(n). 

### How to use

- 所有切糕问题都可以完全不讨论边界, 直接用Integer.MAX_VALUIE来做, 但注意一下1011, 不用边界很可能需要在check之中查一下是否OK. 另外OA可以直接用, 提升效率, 但面试的时候有的面试官不认可, 不认可的话他会告诉你, 告诉你之后你就主动改成边界讨论版就好, 不要跟他理论. 或者你觉得有的题边界讨论很简单, 那直接讨论一下也可以. **总之讨论边界版是常规做法, 不讨论边界是取巧.**
- 切糕问题分三个部分, 讨论边界, binary search 和 check, 其中 前两步都不应该产生过度思考, 可以通过刷题/背模板达到通透. 思考应该在check那一步, 要确保一下check那一步正确即可. 


### 1011. Capacity To Ship Packages Within D Days

- 无边界讨论版

```java
class Solution {

    public int shipWithinDays(int[] weights, int days) {
        int left = 1, right = Integer.MAX_VALUE;
 
        while(left<right){
            int mid = left + (right - left) /2;

            if(check(weights, days, mid)){
                right = mid;
            } 
            else{
                left = mid + 1;
            }
        }
        return left;
    }
    
    private boolean check(int[] weights, int days, int mid){
        int need = 1, sum = 0;
        for(int weight: weights){
            // 如果一开始不讨论边界, 则必须加这一步来保证能装下每一个货物
            if(weight > mid){
                return false;
            }
            sum += weight;
            if(sum > mid){
                sum = weight;
                need ++;
            }
        }
        return need <= days;
    }
}
```

- 讨论边界版

```java 
class Solution {
    // We binary search the final result.
    // The left bound is max(A), the min capacity can be no less than the max weight. 
    // The right bound is sum(A). the max capacity can be no more than total sum. 
    public int shipWithinDays(int[] weights, int days) {
        int left = 0, right = 0;
        // build our left and right boundary. 
        for(int weight: weights){
            // O(n)
            right += weight;
            left = Math.max(left, weight);
        }
        
        while(left<right){
            int mid = left + (right - left) /2;

            if(check(weights, days, mid)){
                right = mid;
            } 
            else{
                left = mid + 1;
            }
        }
        return left;
    }
    
    private boolean check(int[] weights, int days, int mid){
        int need = 1, sum = 0;
        for(int weight: weights){
            sum += weight;
            if(sum > mid){
                sum = weight;
                need ++;
            }
        }
        return need <= days;
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

Maximize Smallest Sum.  这道题算是这个系列里最难的, lower 和upper bound都能解.  想不明白可以看一下这个[解释](https://leetcode.com/problems/divide-chocolate/discuss/1658195/Java-Almost-identical-to-410-1011-Just-Binary-Search)和 类似的切排骨1891

一开始想了半天, 还是没想明白为什么是curr = 0.  直到看到评论区

> Since we are looking for minimum now, it is okay if subarray sum greater. In no. 410, if the sum of subarray section greater than target is forbidden, since the target is our maximum sum of subarray. However, in this problem, we look for the lower bound and make sure the number of splits is correct. Thus, when total > target we treat the new element in the same subarray so we reset total = 0. On the other hand, we need separate the new element in another subarray so we set total = num. 


- upper bound

```java
class Solution {
    public int maximizeSweetness(int[] nums, int k) {

        int left = 0, right = Integer.MAX_VALUE; 
        while(left < right){
            int mid = left + (right - left) / 2 + 1; 
            if (check(mid, nums, k + 1)){
                left = mid;
            }else{
                right = mid - 1;
            }
        }

        return left; 
    }

    private boolean check(int mid, int[] arr, int k){
        int count = 0, sum = 0;
        for (int n : arr){
            sum += n;
            if (sum >= mid){
                sum = 0;
                count++;
            }
        }

        return count >= k; 
    }
}
```

- upper bound count = 1

对我来说 count = 1 的写法更make sense, 因为巧克力的其实状态是一整块.

```java
class Solution {
    public int maximizeSweetness(int[] nums, int k) {

        int left = 0, right = Integer.MAX_VALUE; 
        while(left < right){
            int mid = left + (right - left) / 2 + 1; 
            if (check(mid, nums, k + 1)){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        return left; 
    }

    private boolean check(int mid, int[] arr, int k){
        int count = 1, sum = 0;
        for (int n : arr){
            sum += n;
            if (sum >= mid){
                sum = 0;
                count++;
            }
        }
        // 2022-04-13: 我想了一个多小时还是没想明白此处为什么是<=k, 
        		而不是 < k. 
        return count <= k; 
    }
}
```

- lower bound 微调版

```java
class Solution {
    public int maximizeSweetness(int[] nums, int k) {

        int left = 0, right = Integer.MAX_VALUE; 
        while(left < right){
            int mid = left + (right - left) / 2; 
            if (check(mid, nums, k + 1)){
                left = mid + 1;
            }else{
                right = mid;
            }
        }

        return left - 1; 
    }

    private boolean check(int mid, int[] arr, int k){
        int count = 0, sum = 0;
        for (int n : arr){
            sum += n;
            if (sum >= mid){
                sum = 0;
                count++;
            }
        }

        return count >= k; 
    }
}
```

- lower bound 微调 count = 1

```java
class Solution {
    public int maximizeSweetness(int[] nums, int k) {

        int left = 0, right = Integer.MAX_VALUE; 
        while(left < right){
            int mid = left + (right - left) / 2 + 1; 
            if (check(mid, nums, k + 1)){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        return left; 
    }

    private boolean check(int mid, int[] arr, int k){
        int count = 1, sum = 0;
        for (int n : arr){
            sum += n;
            if (sum >= mid){
                sum = 0;
                count++;
            }
        }
        return count <= k; 
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

### 1891. Cutting Ribbons

这道是 **Upper bound**, 重要!!!

- 边界错误版

```java
class Solution {
    public int maxLength(int[] ribbons, int k) {
        // int sum = 0;
        // for(int rib : ribbons){
        //     sum += rib;
        // }
        // if(sum < k){
        //     return 0;
        // }
        int left = 1, right = (int)1e5;
        int count = 0;
        while(left < right){
            int mid = right - (right - left) / 2;
            count = 0;
            for(int rib : ribbons){
                count += rib / mid;
                if(count > k){
                    break;
                }
            }
            //System.out.println(mid + " " + count);
            if(count < k){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        
//         if(count < k){
//             return 0;
//         }
        return left;
    }
}
```

- upper bound 清晰版

```java
class Solution {
    public int maxLength(int[] ribbons, int k) {

        int left = 0, right = (int)1e5;
        int count = 0;
        while(left < right){
            int mid = right - (right - left) / 2;
            count = 0;
            for(int rib : ribbons){
                count += rib / mid;
                if(count >= k){
                    break;
                }
            }

            if(count < k){
                right = mid - 1;
            }else{
                left = mid;
            }
        }
        
        return left;
    }
}
```

- lower bound 微调版

```java
class Solution {
    public int maxLength(int[] ribbons, int k) {
        int left = 1, right = (int)1e5 + 1;
        while(left < right){
            int mid = left + (right - left) / 2;
            int count = 0;
            for(int rib : ribbons){
                count += rib / mid;
                if(count >= k){
                    break;
                }
            }
            if(count >= k){
                left = mid + 1;
            }else{
                right = mid;
            }
        }
        
        return left - 1;
    }
}
```

- left <= right 版

答案来自[此处](https://leetcode.com/problems/cutting-ribbons/discuss/1901738/Java-Solution-With-Explanation)

```java
class Solution {

    public int maxLength(int[] ribbons, int k) {
        int sum = 0, r = 0, l = 1, ans = 0;
        
        for(int ribbon : ribbons) { //Getting our last value of the search space
            r = Math.max(ribbon, r);
        }
        
        while(l <= r) { //Binary Search
            int mid = l + (r-l)/2; //Our current length in the search space
            sum = 0;
            for(int ribbon : ribbons) { //The number of segments obtained with mid length
                sum += ribbon/mid;
            }
            if(sum >= k) { //Maybe there's a better answer, move to the right
                ans = mid;
                l = mid+1;
            } else  { //No way to obtain k segments, move to the left
                r = mid-1; 
            }
        }
        return ans;
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

## 题目

- 简单入门: 1011, 875, 1482
- 核心: 1095, 410, 1891
- 难: 1231
- 偏: 774, 540

## 刷题策略

- binary search的重点是根据模板把核心刷透, 类型题能够做到一遍过. 面试中不需要反复过度思考. 
- 偏题难题可以先放, 1231这种题目很好, 理解其中一个解法即可. 至于774这类题目就等着面特定公司刷高频即可. 

## Asheley 解法

### LC 34

```python
class Solution(object):
    def searchRange(self, nums, target):
        n = len(nums)
        
        left, right = -1, -1
        
        l, r = -1, n
        while l + 1 < r: # find left [1 | 2 2 3]
            mid = l + (r - l) // 2
            if nums[mid] < target:
                l = mid
            else:
                r = mid
        if r < n and nums[r] == target:
            left = r
        
        l, r = -1, n
        while l + 1 < r: # find left [1 2 2 | 3]
            mid = l + (r - l) // 2
            if nums[mid] <= target:
                l = mid
            else:
                r = mid
        if l > -1 and nums[l] == target:
            right = l
        
        return [left, right]
```

### LC 1095

```python

class Solution(object):
    def findInMountainArray(self, target, MountainArray):

        n = MountainArray.length()
        
        l, r = 0, n-1 # FIND TOP
        while l + 1 < r:
            mid = l + (r-l) // 2
            mid_v = MountainArray.get(mid)
            mid_pre_v = MountainArray.get(mid-1)
            if mid_pre_v <= mid_v:
                l = mid
            else:
                r = mid
        top = l
        
        l, r = -1, top + 1 # FIND LEFT
        while l + 1 < r: 
            mid = l + (r-l) // 2
            mid_v = MountainArray.get(mid)  # [1 2 | 3 3 4 5]
            if mid_v < target:
                l = mid
            elif mid_v >= target:
                r = mid
        if r < top + 1 and MountainArray.get(r) == target:
            return r
        
        l, r = top, n # FIND RIGHT
        while l + 1 < r: # [5 |  3 3 1]
            mid = l + (r-l) // 2
            mid_v = MountainArray.get(mid)  
            if mid_v <= target:
                r = mid
            elif mid_v > target:
                l = mid
        if r < n and MountainArray.get(r) == target:
            return r
        
        return -1
```




## 其他

- [ShawnLyu](https://shawnlyu.com/algorithms/binary-search-find-upper-and-lower-bound/)
- 期中考试, 5月8日之后再上