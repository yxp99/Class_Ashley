---
layout: post
title: Topological Sort
date: 2022-05-25
author: Shadow Song
tags: [Graph, Algorithm]
toc: true
comments: true
---

## When to use

- Use to find cycles in directed graph. 
- cannot apply to undirected graph. 


## Algorithm

1. 便利每一个节点，根据输入来.  
	a. 并记录每一个节点的入度map<节点，入度的值>.  
	b. 每一个节点指向的节点map<节点，［指向的节点］>.  
2. 把入度为0的节点放入queue中. 
3. BFS.   
	a. 从queue中pop元素，counter＋＋.  
	b. 查看当前节点指向的节点，并把指向的节点的入度的值减1，并把指向的节点的入度为0的元素放入queue中.  
	c. 重复i直到queue为空.  
	
![](https://s3.ap-south-1.amazonaws.com/afteracademy-server-uploads/topological-sorting-illustration-5981c773c98e37bc.png)

## 207. Course Schedule

首先就看经典的207题, 下面代码是纯粹手敲的, 注释也是自己写的.  


```java
class Solution {
    
    public boolean canFinish(int numCourses, int[][] prereq) {
        if(prereq.length == 0)
            return true;
        
        // For graph key as node val, value as all the nodes it points to. 
        // 其实这里无论用set 还是 linkedlist都没有任何区别. 
        Map<Integer, Set<Integer>> graph = new HashMap<>();
        // For hm, key as node val, value as its Node Indegree
        Map<Integer, Integer> hm = new HashMap<>();
        
        // 1. Traverse every Node. 
        for(int[] arr: prereq){
            // 1a. record every node and the nodes it points to. 
            Set<Integer> hs = graph.getOrDefault(arr[1],new HashSet());
            hs.add(arr[0]);
            graph.put(arr[1], hs);
            if(!graph.containsKey(arr[0]))
                graph.put(arr[0], new HashSet());
            // 1b. record every node and its Node Indegree
            hm.put(arr[0], hm.getOrDefault(arr[0],0) + 1);
            if(!hm.containsKey(arr[1]))
                hm.put(arr[1],0);
        }
        
        // 2. Init a Queue. Put all the nodes that have Node Indegree 0 into the queue. 
        Deque<Integer> q = new ArrayDeque<>();
        for(Map.Entry<Integer, Integer> entry: hm.entrySet()){
            if(entry.getValue() == 0)
                q.offer(entry.getKey());
        }
        
        int count = 0;
        
        // 3. While q is not empty, poll. 
        while(!q.isEmpty()){
            int pick = q.poll();
            count ++;
            for(int course: graph.get(pick)){
                // 3a. For polled element, reduce all its linked nodes' Node Indegree value by 1. 
                hm.put(course, hm.getOrDefault(course, 1)-1);
                if(hm.get(course)==0)
                    q.offer(course);
            }
        }

        // 4. If the nodes we polled is not equal to graph size(most likely less than or equal to), then false. 
        return count==graph.size();
    }
}
```



## Problems 

- 核心: 207, 210. 
- 其他: 444
- 难:  269