---
layout: post 
title: Two Pointers & Sliding Window
date: 2021-11-22
author: Shadow Song
tags: [上课记录,SlidingWindow, TwoPointers]
toc: true
comments: true
---


## 相向指针

- 和Sliding Window的区别, 就是双指针, 只取两个pointer 的位置, 但 Sliding window 会考虑他们之间所有的值. 


### Two Sum II - Input array is sorted LC_167

**Problem Description**

[LC167](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)

**Code**

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        
        int low = 0; 
        int high = numbers.length-1;
        
        while (high > low){
            if(numbers[low] + numbers[high]==target){
                return new int[] {low+1, high+1};
            }
            else if(numbers[low] + numbers[high] > target){
                high --;
                    
            }
            else{
                low ++;
            }
        }
        
        return new int[] {0,0};
        
    }
}
```

### 3Sums LC15

其实就是按照2SumII的双指针去进行的, 区别是要引入第三个量, 做一些相应的调整. 其实也就是用第三个量卡一下而已. 增加一层维度. 

需要注意的是, 下面加注释的部分, 其他看leetcode标准答案就好, 有动画, 说的很详细了, 不再赘述.

**code**

```java
class Solution {
    List<List<Integer>> output;
    public List<List<Integer>> threeSum(int[] nums) {
        output = new ArrayList<>();
        Arrays.sort(nums);
        
        // nums[i]<=0 的这个条件是因为, 双指针是向右卡的, low 和high的相应值都大于i. 
        // 所以一旦大于0就不可能有解了, 没必要继续traverse下去. 
        for(int i=0; i<nums.length-1 && nums[i]<=0; i++){
            if(i==0 || nums[i]!=nums[i-1]){
           	    // 上面这个if只有一个目的, 就是去重. 去重的原因是这道题的条件不让重复. 
           	    // 去重的原理是之前的sort, 因为sort, 所以只要查相邻是不是重复即可. 
                twoSum(nums, i);
            }
        }
        return output;
    }
    
    private void twoSum(int[] nums, int i)
    {
        //System.out.println(nums[i]);
        
        int curr = nums[i];
        int low = i+1;
        int high = nums.length-1;
        //System.out.println(curr + " " + nums[low] + " " + nums[high]);
        
        
        while(low<high)
        {
            int sum = curr + nums[low] + nums[high];
            if(sum>0){
                high--;
            }else if(sum<0){
                low ++;
                //System.out.println(nums[low]);
            }else{
                List<Integer> list = new ArrayList<>();
                list.add(nums[i]);
                list.add(nums[low++]);
                list.add(nums[high--]);
                output.add(list);
                // 这个while loop是twoSumII里没有的. 
                // 加这个的原因是, 这道题不是唯一解的, 就算两个指针卡到了, 也可能往下继续卡有其他解. 
                // 之前twoSumII没有加这个while loop调整指针继续往下卡的原因是twoSumII的题就设定是单解. 
                while(low<nums.length-1 && nums[low] == nums[low-1]){
                    low++;
                }
            }
        }
    }
}
```
### Container With Most Water LC_011

**Problem Description**

题目不放了, 因为需要看图才能看懂, 直接去[LeetCode](https://leetcode.com/problems/container-with-most-water/)上面看吧. 

**Algorithm**

这道题我拿来首先想到的就是dynamic programming, 但越想越觉得不符合dynamic的求和原则, 因为需要考虑之前的所有边.   

算法是Sliding Window 这一次的游标卡尺是从首尾两端开始卡, 慢慢往里面收. 由于题目的特殊性而使用木桶原理.  

**这种特殊的题目没什么好说的, 唯有刷脸.**

<br>

**Code**

```java
class Solution {
    public int maxArea(int[] height) {
        int maxArea = 0, left = 0, right = height.length -1;
        
        while(right>left)
        {
            maxArea = Math.max(maxArea, Math.min(height[left], height[right])*(right-left));
            //System.out.println(left + " , " + right + " , " + maxArea);
            if(height[left]>height[right])
            {
                right--;
            }
            else
            {
                left ++;
            }
        }
        return maxArea;
    }
}
```


## 同向指针

### LC581_Shortest Unsorted Continuous Subarray

```java
class Solution {
    private Integer low;
    private Integer high;
    int n;
    public int findUnsortedSubarray(int[] nums) {
        this.n = nums.length;
        int[] sNums = nums.clone();
        Arrays.sort(sNums);
        
        for(int i=0; i<n; i++)
        {
            if(nums[i]!=sNums[i]){
                low = i;
                break;
            }                
        }
        if(low == null)
            return 0;
        
        for(int i=low+1; i<n; i++)
        {
            if(nums[i]<sNums[i]){
                high = i;
            }  
        }

        
        return high-low+1;
    }
}
```

- Time complexity : O(nlogn). Sorting takes  nlogn time.

- Space complexity : O(n). We are making n copy of original array.


### LC438_Find All Anagrams in a String

这道题挺有意思的, 不算特别吧. 思路其实想想也不难, 看答案不到10分钟就能懂.  这道题关键的是这个思路, **HashMap是可以比较的**, 有了这个思路之后很多题都可以利用这个思路比,**尤其是那种乱序的比较, 只比较元素不比较顺序的, 都可以用这个思路**: 

- key 和 value完全一样的两个HashMap就是完全相同的, 
- key, value=0 和 没有这个key和value是两回事, 不相等. hashmap, key = a, value = 0. 和 hashmap b, 没有key = a 这是两回事. 

官方答案太啰嗦, 自己改了改, 放在下面. 


```java
class Solution {
  public List<Integer> findAnagrams(String s, String p) {
    int ns = s.length(), np = p.length();
    if (ns < np) return new ArrayList();

    Map<Character, Integer> pMap = new HashMap();
    Map<Character, Integer> sMap = new HashMap();
    // build reference hashmap using string p
    for(char c: p.toCharArray()){
        pMap.put(c, pMap.getOrDefault(c,0) + 1);
    }

    List<Integer> output = new ArrayList();
    // sliding window on the string s
    for (int i = 0; i < ns; ++i) {
      // add one more letter 
      // on the right side of the window
      char c = s.charAt(i);
      sMap.put(c, sMap.getOrDefault(c,0) + 1);
      // remove one letter 
      // from the left side of the window
      if (i >= np) {
        char ch = s.charAt(i - np);
        if (sMap.get(ch) == 1) {
          sMap.remove(ch);
        }
        else {
          sMap.put(ch, sMap.get(ch) - 1);
        }
      }
      // compare hashmap in the sliding window
      // with the reference hashmap
      if (pMap.equals(sMap)) {
        output.add(i - np + 1);
      }
    }
    return output;
  }
}
```

### Longest Substring Without Repeating Characters LC_03

**Problem Description**

Given a string s, find the length of the longest substring without repeating characters.

Example 1:

> Input: s = "abcabcbb"  
> Output: 3  
> Explanation: The answer is "abc", with the length of 3.

Example 2:

> Input: s = "bbbbb"  
> Output: 1  
> Explanation: The answer is "b", with the length of 1.

**Code**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int n = s.length();
        Set<Character> hs = new HashSet<>();
        int answer=0, i=0, j=0;
        
        while(i<n && j<n)
        {
            if(!hs.contains(s.charAt(j)))
            {
                hs.add(s.charAt(j));
                j++;
                if(hs.size()>answer)
                {
                    answer = hs.size();
                }
            }
            else{
                hs.remove(s.charAt(i));
                i++;
            }
        }
        return answer;
    }
}
```

**Algorithm**

其实这个算法并不难, 反而比较实际, 容易理解.  把Set hs想象成一把游标卡尺, 卡尺的左端是i, 右端是j. 
假设我们有一个String  ABCDAE

从A点出发, i和j都在0, 也就是A, 只要下一个char没有重复, 游标卡尺的右端就不断往下一个char走. 如上面的例子, 游标卡尺走到ABCDA, j = 4的时候会停住.  这个时候从A点出发的不重复subString已经到了Max. 这个时候把尺子右端不动, 把卡尺左端往后挪一位. 我们得到 BCDA. 之后下一位还是不重复的E, 再挪右端j, 左端i不动. 得到BCDAE. 这个过程中keep tracking 卡尺的max长度. 最后得到答案5. 

两点: 

1. 一开始会考虑要不要向前track, 其实想一下完全没有必要. 这个理解就是和DP是一样的, 因为之前的元素已经被卡尺求过了, 没有必要向前. 所有的subString都是向后track. 
2. 一开始是在想是不是要在每个字母上卡一下, 是不是一旦出现了重复元素之后, 就立刻要把尺子收紧把左端右端合在一起然后重新测量, 会把else写成这样: 

	```java
	else{
	    hs = new HashSet<>();
	    i++;
	    j=i;
	}
	```
	
	举个例子: ABCBD 这个, 一开始尺子卡在ABC, 然后在B发现重复, 尺子收起来, 从B(i=1)开始重新卡. 我会这么想, 其实这么想也比较直观. 
	
	其实实际上没有必要. **关键在于这个尺子里面的元素是不可重复的**, 也就是说尺子内部的元素是绝对干净的. 这个时候没有必要收尺子, 尺子左端已经在正确的位置, 只要继续挪动右端就可以了. 
	
还有一些更快的解法, 游标卡尺加上hashmap的解法.  **用hashmap记录位置, 加上一个尺子收紧的方法**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if(s.length() == 0 || s.length() == 1) return s.length();
        Map<Character, Integer> hm = new HashMap<>();
        int output = 0, j = 0;
        for(int i=0; i< s.length(); i++){
            char ch = s.charAt(i);
            if(hm.containsKey(ch)){
                int lastPosition = hm.get(ch);
                if(j<lastPosition) {
                    j = lastPosition;
                }
            }
            output = Math.max(output, i - j + 1);
            hm.put(ch, i + 1);
        }
        return output;
    }
}
```



## 快慢针

- 用法比较单一, Linkedlist, 提取index

### LC876_Middle of the Linked List

一道很纯粹的快慢针问题. 快针是慢针速度的两倍.  纯粹的太简单了.  有兴趣可以看一下148题, 比这个稍微复杂一些, 需要处理edge case. 

**code**

```java
public ListNode middleNode(ListNode head) {
    ListNode slow = head, fast = head;
    while(fast!=null && fast.next!=null)
    {
        slow = slow.next;
        fast = fast.next.next;
    }
    return slow; 
}
```

### LC19_Remove Nth Node From End of List

一道在LinkedList上的快慢指针. 一开始看很简单, 其实不然. 思路很简单, 但具体edge case很特殊, 不好处理. 需要考虑的是只有一个元素和remove head的情况, 所以加装这个dummy head. 

**19题, 1721题, 1474 这三道题同宗同源**. 其中最难的是19题, 需要考虑的稍微多一些.  如果要刷的话, 就刷19题吧. 

**code**

```java
public ListNode removeNthFromEnd(ListNode head, int n) {
	 // 这个dummyHead不是没有意义的. 加装dummyHead的意义是为了处理remove head和只有一个元素的情况. 
	 // 这个算法必须有三个元素的时候才有用.  加装dummyhead之后, 在有一个元素的情况下是, dummyHead, head, null(可以允许一个null)
	 // 本身思路并不难, edge case 很不好处理. 
    ListNode dummy = new ListNode(0);
    dummy.next = head;
    ListNode first = dummy;
    ListNode second = dummy;
    // Advances first pointer so that the gap between first and second is n nodes apart
    for (int i = 1; i <= n + 1; i++) {
        first = first.next;
    }
    // Move first to the end, maintaining the gap
    while (first != null) {
        first = first.next;
        second = second.next;
    }
    second.next = second.next.next;
    return dummy.next;
}
```

## 两种解法

- Deque: 前后都可以出. 
- index 指针, **它更聪明, 一定更省空间**. 

## 难题

### 239. Sliding Window Maximum

**重点.** 很巧妙直接用deque解的方法. 在加入之中进行删减, 造成第一个元素永远为最大的情况.  难点在于用deque去记录index, 而不是元素本身.

下面的答案是从标准答案来的, 改成了自己的习惯, 并且加了注释, 看五分钟就能看懂.  

```java
class Solution {
    int[] nums;
    Deque<Integer> deq = new ArrayDeque<>();
    
    private void clean(int i,int k){
        // remove indexes of elements not from sliding window
        // Keep only the indexes of elements from the current sliding window.
        // 其实也就是查一下第一个元素是不是我们需要剔除的边际元素. 
        if(!deq.isEmpty() && deq.getFirst() == i-k)
            deq.removeFirst();
        
        // remove from deq indexes of all elements 
        // which are smaller than current element nums[i], since they will not be the maximum ones.
        // 因为这些数都比nums[i] 小, 而且都在nums[i] 左边, 所以没必要留着, 因为window是从左往右的, 他们一定会在nums[i]之前被刷掉. 
        while(!deq.isEmpty() && nums[i]>nums[deq.getLast()])
            deq.removeLast();
    }
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        
        // edge cases. 
        if(n*k==0) return new int[0];
        if(k==1) return nums;
        int[] output = new int[n-k+1];
        this.nums = nums;
        
        // First initial phase. from 0 to k. 
        for(int i=0; i<k; i++){
            clean(i,k);
            deq.addLast(i);
        }
        output[0] = nums[deq.getFirst()];
        
        for(int i=k; i<n; i++){
            clean(i,k);
            deq.addLast(i);
            // The first element of deque is the our max value. 
            // deq的第一个就是max.
            output[i-k+1] = nums[deq.getFirst()];
        }
        return output;
    }
}
```

### 992. Subarrays with K Different Integers

一道比较喜欢的滑动窗口难题. 滑动窗口可以考的很难. 下面这道就是. 

**下面这个用HashMap统计个数的技巧需要掌握**, 最难的点在于理解为什么 output+= i-j+1; 其实就是拆分的排列组合公式, 解释放在了最下面. 

```java
class Solution {
    public int subarraysWithKDistinct(int[] nums, int k) {
        // 如果要算exact k 的话, 就用 至多 k 减去至多 k - 1. 
        // 这步不难理解, 稍微想一会儿就能想通. 
        return atMost(nums, k) - atMost(nums, k-1);
    }
    
    public int atMost(int[] nums, int k){
        // hm用来统计个数,  key 是 int in nums,  val 是 key int 出现的frequency. 
        Map<Integer, Integer> hm = new HashMap<>();
        
        // i, j 是两个滑动窗口, 保证 i j 之间的differnt 不能超过k 个. 
        int j=0, output = 0, n = nums.length;
        for(int i=0; i<nums.length; i++){
            // 统计个数, 出现就放进 hm 里
            hm.put(nums[i], hm.getOrDefault(nums[i], 0) + 1);
            
            // 如果hm size  大于 k, i-j之间的different  int  个数已经大于 k. 
            // 这是我们需要滑动 j 向右, 减少 different  int  个数. 
            while(hm.size() > k){
                // 将当前 j上的数字移除 hm
                hm.put(nums[j], hm.get(nums[j]) - 1);
                if(hm.get(nums[j]) <= 0){
                    // if val小于等于 0, 说明这个 数已经不存在了, 移除 hashmap. 
                    hm.remove(nums[j]);
                }
                j++; // j向右一位. 
            }
            output += i - j + 1;  // 这一步是排列组合公式来的. 需要看下面的更多解释. 
        }
        return output;
    }
}



More explanation on output += i - j + 1; 

step 1. find all subarrays that has most K odd number   (1,2,3 ...K)
step 2. find all subarrays that has most K-1 odd number  (1,2,3,...K-1)
step 3. find step 1 minus step 2 will result in subarrays only has K odd numbers


example
arr=[1,2,1,2,3]
K=2

when K=2
i           j-i+1          contiguous subarray
0            1               [1]
1            2               [1,2], [2] 
2            3               [1,2,1], [2,1], [1]
3            4               [1,2,1,2], [2,1,2], [1,2], [2]
4            4               [2,1,2,3], [1,2,3], [2,3], [3]
total                         14 subarrays

when K=1
i           j-i+1          contiguous subarray
0            1               [1]
1            2               [1,2], [2] 
2            3               [2,1], [1]
3            4               [2,1,2], [1,2], [2]
4            4               [2,3], [3]
total                        10 subarrays

when K=2 minus when K=1 is 4 subarrays
[1,2,1], [1,2,1,2], [2,1,2,3], [1,2,3]
```

## 总结

### 什么时候用

- 基本上是Array, 可以说SlidingWindow一定是array.  然后twoPointer不一定. 
- 不能存在求和关系. 
- 一段区间之内, 产生连续关系的时候, 使用SlidingWindow

### 边界条件

- 针是不是碰到了边界.  j<n
- i < j 
- 同向情况: 可以需要考虑的一个因素, 每一次, j必往右走一步, i走不走, 情况判断. 这样可以尽量保证, j在i右边.  

### 技巧

- HashMap常用, 记录 freq, 记录 index


## 作业

- **3**, 11, 167, 15, **438**, 581, 26, 42(难), 763
- 876, 19, **239**, 992. 239题可以自己写一下指针的解法版本. 
- **215** QuickSelect 重要. 

## 其他

- 如果没有会员的话, 可以跟我借个leetcode账号. 

## Commitment

- 我三个月之内是可以教的.  但是假设就算我哪天真不想了, 我有契约精神, 我至少会告知你之后, 再教三节课. 
- 你的话, 不想学随时可以跟我说. 






