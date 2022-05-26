---
layout: post
title: Basic Graph
date: 2022-05-25
author: Shadow Song
tags: [Graph]
toc: true
comments: true
---

## Construction

![](https://lh3.googleusercontent.com/pw/AM-JKLXXjHARmQzq91ysHWFq45ytoaiI0x99OQyn6pAoIHP515_hT1XlGZF3VWzNk0yHuM0VCMnsPz4K8pb9ZhEDoTcfVJwbtn5DrVp78kQ-k1YLGGSoa4K5jqkyDH9pAGsBrElCZjE2fxCHiEx7h70Hp-Wy=w500-h730-no?authuser=0)

```java
// Build a hashmap graph
Map<Integer, List<Edge>> graph = new HashMap<>();
for(int i = 1; i <= n; i ++){
    graph.put(i, new ArrayList<>());
}
for(int[] time: times){
    graph.get(time[0]).add(new Edge(time[1], time[2]));
}
    
 class Edge{
    int con, dist;
    public Edge(int con, int d){
        this.con = con;
        this.dist = d;
    }
}	
```

## dfs Traverse

- Leetcode 323

## Directed/Undirected graph

