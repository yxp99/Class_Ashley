---
layout: post 
title: BackTrack
date: 2022-02-18
author: Shadow Song
tags: [上课记录]
toc: true
comments: true
---


## 刷题顺序

## 简单

- 77 Combinations
- 78 Subsets
- 46 Permutations

### 核心

Backtrack的核心是三道题. 这三道题覆盖了很多其他题目.

- 40 Combination Sum II  (组合)
- 47 Permutations II  (排列)
- 90 Subsets II  (组合). 


### 好题

好题是难度跟核心类似但普遍性稍差的题目. 

- 131 Palindrome Partition 这道本质不难. 就是复合题.  i+1组合复杂条件判断组合双指针. 
- 698 Partition to K Equal Sum Subsets. 跟上面的131一样, 也是i+1条件判断跳跃. 只不过这道更难更复杂. 目前在条件判断跳跃之中, 这道是最难的.   可以理解为更加复杂的40题. 40题如果刷熟了的话可以刷这道. 

## 栗子

### 39 Combination Sum

```java
class Solution {
    List<List<Integer>> output = new LinkedList<>();
    public List<List<Integer>> combinationSum(int[] list, int target){
        Arrays.sort(list);
        dfs(list, target, 0, new LinkedList<>(), 0);
        return output;
    }

    private void dfs(int[] list, int target, int start, LinkedList<Integer> numList , int sum){
        
            if(sum == target){
                output.add(new LinkedList<>(numList));
              return;
            }

          for(int i=start; i<list.length; i++){    
            sum += list[i];
            if(sum>target)
                break;
              
             numList.addLast(list[i]);
            dfs(list, target, start++, numList, sum);
            sum = sum - list[i];
            numList.removeLast();
          }

    }
    
}
```

### 78 Subsets

```java
class Solution {
    List<List<Integer>> output = new LinkedList<>();
    int[] nums;
    int N;
    public List<List<Integer>> subsets(int[] nums) {
        this.nums = nums;
        this.N = nums.length;
        output.add(new LinkedList<>());
        dfs(0, new LinkedList<Integer>());
        return output;
    }
    private void dfs(int start, LinkedList<Integer> numList)
    {
    	output.add(new LinkedList<>(numList));

        for(int i = start; i<N; i++){
            numList.add(nums[i]);
            dfs(++start, numList);
            numList.removeLast();
        }
    }
}
```

### 90 Subsets II

```java
class Solution {
    int[] nums;
    List<List<Integer>>  output = new LinkedList<>();
    
    public List<List<Integer>> subsetsWithDup(int[] nums) {
        this.nums = nums;
        // 必须sort, 才能去重. 
        Arrays.sort(nums);
        output.add(new LinkedList<>());
        dfs(0, new LinkedList<>());
        return output;
    }

    private void dfs(int start, LinkedList<Integer> numList){
        
        
        for(int i = start; i < nums.length; i++){
            if(i != start && nums[i] == nums[i-1]) 
            	// 注意这个去重不是绝对的去掉重复元素(那样应该用set). 这个去重是在sort之后保证每一层不出现重复元素. 
            	// 每一层的意思是把递归想象成一个Tree, BFS的每一层这个意思. 
                continue; // skip duplicates
            
            numList.add(nums[i]);
            output.add(new LinkedList<>(numList));
            dfs(i + 1, numList);
            numList.removeLast();
        }
    }
    
}
```

### 40 Combination Sum II

关键: 

- 一开始要去重, 通过去重避免重走相同元素. 去重分两步, 一开始要sort, 之后backtrack的时候要查跟前一个元素是否一样.
- Backtrack的时候要 用i+1, 而不是start + 1

```java
class Solution {
    List<List<Integer>> output = new LinkedList<>();
    int target;
    int[] nums;
    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        this.nums = candidates;
        this.target = target;
        Arrays.sort(nums);
        dfs(0, new LinkedList<>(), 0);
        return output;
    }
    
    private void dfs(int start, LinkedList<Integer> numList, int sum)
    {
        if(sum == target){
            output.add(new LinkedList<>(numList));
            return;
        }
        
        for(int i=start; i<nums.length; i++){
            if(i!=start && nums[i] == nums[i-1])
                continue;
            if(sum+nums[i]>target)
            	// 这个if要放这里, 不要放上面. 因为是sorted array, 放这里可以直接打破后面所有的, 大幅度提升算力. 
                continue;
            numList.add(nums[i]);
            dfs(i+1, numList, sum+nums[i]);
            numList.removeLast();
        }
    }
}
```

### 46

- Set 解法

```java
class Solution {
    List<List<Integer>> output = new LinkedList<>();
    int[] nums;
    public List<List<Integer>> permute(int[] nums) {
        this.nums = nums;
        dfs(new HashSet<>(), new LinkedList<>());
        return output;
    }
    private void dfs(HashSet<Integer> hs, LinkedList<Integer> numList){
        if(numList.size() == nums.length){
            output.add(new LinkedList<>(numList));
            return;
        }
        
        for(int i=0; i<nums.length; i++){
            if(hs.contains(nums[i]))
                continue;
            hs.add(nums[i]);
            numList.add(nums[i]);
            dfs(hs,numList);
            numList.removeLast();
            hs.remove(nums[i]);
        }
    }
}
```

- Swap 解法

```java
class Solution {
    private List<List<Integer>> output; 
  public void backTrack(int start, List<Integer> nums) {
    // if all integers are used up
    if (start == nums.size() -1){
         //!!! 此处需要克隆一个新的nums, 如果直接add(nums), 会造成reference, 
        //nums不是primitive type之后会被更改. 如果直接用nums会导致所有都会指向同一个nums.
        output.add(new ArrayList<Integer>(nums));  

        return;
    }
      
    for (int i = start; i < nums.size(); i++) {
      // place i-th integer first 
      // in the current permutation
      Collections.swap(nums, start, i);
      // use next integers to complete the permutations
      backTrack(start+1, nums);
      // backtrack
      Collections.swap(nums, start, i);
    }
  }

  public List<List<Integer>> permute(int[] nums) {
    // init output list
    output = new ArrayList<>();

    // convert nums into list since the output is a list of lists
    List<Integer> nums_lst = new ArrayList<>();
    for (int num : nums)
      nums_lst.add(num);

    backTrack(0, nums_lst);
    return output;
  }
}
```

## 47 Permutations II

```java
class Solution {
    List<List<Integer>> output = new ArrayList<>();
    int N;
    public List<List<Integer>> permuteUnique(int[] nums) {
        
        // count the occurrence of each number
        HashMap<Integer, Integer> counter = new HashMap<>();
        N = nums.length;
        for (int num : nums) {
            counter.put(num, counter.getOrDefault(num, 0) + 1);
        }

        this.backtrack(new LinkedList<>(), counter);
        return output;
    }

    private void backtrack(LinkedList<Integer> numList, HashMap<Integer, Integer> counter) {

        if (numList.size() == N) {
            // make a deep copy of the resulting permutation,
            // since the permutation would be backtracked later.
            output.add(new ArrayList<Integer>(numList));
            return;
        }

        for (Map.Entry<Integer, Integer> entry : counter.entrySet()) {
            Integer num = entry.getKey();
            Integer count = entry.getValue();
            if (count == 0)
                continue;
            // add this number into the current combination
            numList.addLast(num);
            counter.put(num, count - 1);

            // continue the exploration
            backtrack(numList, counter);

            // revert the choice for the next exploration
            numList.removeLast();
            counter.put(num, count);
        }
    }
}
```

## 技巧总结

- Permutation or Combination
- clone
- hashmap technique. 

### start++

start++可以重复使用元素, ++start不可以.  会重复使用后面的元素, 但不会往前走, 例如不会出现2,1的情况. 

如下, 当使用[1,2,3]的时候start ++ 的结果, 看2,3的部分其实不会重复去查1的部分. 可以通过list的长度提取自己需要的output: 

```java
[1]
[1, 1]
[1, 1, 1]
[1, 1, 2]
[1, 1, 3]
[1, 2]
[1, 2, 2]
[1, 2, 3]
[1, 3]
[1, 3, 3]
[2]
[2, 2]
[2, 2, 2]
[2, 2, 3]
[2, 3]
[2, 3, 3]
[3]
[3, 3]
[3, 3, 3]
```

### ++start

跟上面start++的区别就是不会重复使用元素.  其实这个不存在, 见下面的i+1

```java
[1]
[1, 2]
[1, 2, 3]
[1, 3]
[2]
[2, 3]
[3]
```

### i+1

i+1 在正常状态跟上面的++start是一样的. 如下. 但i+1的用途是用在查重过滤的情况的时候就会不一样.  换句话说, 在不是一个一个往前走(跳着走)的情况下, 使用i+1.   **这俩其实是包含关系, 所以++start都可以用i+1替代,但不是所有i+1都能用start替代.**

参考著名的40题和131题, 会使用条件判断跳跃. 

```java
[1]
[1, 2]
[1, 2, 3]
[1, 3]
[2]
[2, 3]
[3]
```

### start+1

这个很少见, 目前只见过46题的swap解法和526题在使用. 没见过第二道. 就不总结了. 


## Complexity Analysis

这一系列题目complexity通常都是一样的.  下面这个来自40题的截图. 


![](https://lh3.googleusercontent.com/pw/ACtC-3c4oY-srWO5rgHXDog4EJgOwDgkPWm7KQZwc5BChp3HETIKyX42dHECkEOMOE16ogL2C0CC1qCIMk2sUAY9FvKvl8A5VJPIfzJjlUsVUdUcyuyXFFkP_XeX9ZTwZlsMZalm-Gh-0pYV4Q-acsbS-p0J=w635-h436-no?authuser=0)

![](https://lh3.googleusercontent.com/pw/ACtC-3cXpes0x0OomgMlI0v6fqanlpM5ywD-NaCTHarbHgOzkN72UOChrdXluAVLKPRsj9ABzzLArSGqjJBNVsJHTHPYidbJGmGLRwc_kAhOb0T9R3SW4nlql5zsCgeYyid8e2NSyYBsJGPZz4-3O7KG8FD0=w617-h576-no?authuser=0)




## 题号

* 78 Subsets (combination)
* 90 Subsets II(必刷)
* 46 Permutations
* 47 Permutations II (必刷)
* 77 Combinations
* 39 Combination Sum (简单)
* 40 Combination Sum II (必刷)
* 216 Combination Sum III
* 377 Combination Sum IV (没做)
* 131 Palindrome Partition
* 132 Palindrome Partitioning II (DP)
* 1278 Palindrome Partitioning III (DP)
* 1745 Palindrome Partitioning IV (DP)
* 267 Palindrome Permutation II (和47题重复)
* 17 Letter Combinations of a Phone Number
* 698 Partition to K Equal Sum Subsets



偏题/难题: 

- 60  Permutation Sequence
- 1048 Longest String Chain

应用题: 

- 1152
- 526 Beautiful Arrangement
- 126

## 总结

这一块是重点, 需要熟练掌握. 纯粹的backtrack由于算力过大其实不常考. 常见的类型是把backtrack融合在题目之中,也就是应用题, 所以一般都不是太难. 

建议第一轮可以把题号上的非难题都刷一遍, 之后只刷三道核心, 三道核心刷熟刷透即可, 这一块没必要大量做题, 也没有必要刷太难的题目. 
