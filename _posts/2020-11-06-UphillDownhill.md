---
layout: post
title: UpHill_DownHill
date: 2020-11-06
author: Shadow Walker
tags: [Algorithm, Interview, Graph, Dijkstra]
toc: true
comments: true
---

这道题是我在2020-8-19在hiretual第一轮面试的时候被考到的题目. 一直想要整理, 拖拖拉拉, 直到脱了两个多月到今天才整理. 

第一轮是一道非常难的图论题, 我当时清楚的记得那道题我直接搜答案, 想根据答案进行改动, 结果硬是一个字没改下来, 最后硬着头皮改了几个变量名称交上去了. 

我当时找的答案的链接现在已经找不到了. 我自己的答案是根据当时答案的逻辑用自己的代码写出来的, 我喜欢用自己的代码, 毕竟可以重复使用, 自己也更熟悉一些.   刚才看到网上有人用[DFS](http://www.liyongzhi.xyz/posts/2020/07/hiretual_interview/)就能做出来, 比我这个简便很多, 不知道是否好用, 我看了几眼, 没有理解, 也不想再看了.  我对DFS的做法表示质疑, DFS如何确保路径最短呢? 很多图论题目因为给的例子简单, 用了一些简单做法确实能得出最优解, 但答案其实未必正确. 

**我自己的解法在****[这里](https://github.com/easonback26/Graph/blob/master/src/Uphill_Downhill.java)**

这道题收录的意义, 在于有一定复杂度的graph问题加上Dijkstra:

1. 这一道题有一定复杂度, 给定的path 已经是directed, 但题目中有给了第二层direction, 即上行和下行的direction. 
2. 在一个graph之中,我们无法表达三维的direction, 所以构建两个graph表达, 上行和下行. 
3. 综合以上两点, 把这道题弄透, 对于graph的整体理解有很大帮助. 
3. Dikstra在这道题之中是很好的应用, 我没有过多的修改直接把代码粘贴进去. 

## 原题

当你看到你的室友比你更有魅力时，你决定每晚跑步，因为他/她经常锻炼。
 
现在你有一本北京地名词典。它的形式是{location:elevation}。在百度地图上找到的连接各个地方的距离数组。  
请找出最短路线的长度，在这条路线上你可以完全上坡，然后完全下坡。假设你住在"Huilongguan"（回龙观），  
字典为elevations = {"Huilongguan": 5, "Chaoyang Park": 25, "National Stadium": 15, "Olympic Park": 20, "Tsinghua University": 10}
路径为：   

```
paths = {   
    ("Huilongguan", "Chaoyang Park"): 10,  
    ("Huilongguan", "National Stadium"): 8,  
    ("Huilongguan", "Olympic Park"): 15,  
    ("Chaoyang Park", "Olympic Park"): 12,  
    ("National Stadium", "Tsinghua University"): 10,  
    ("Olympic Park", "Tsinghua University"): 5,  
    ("Olympic Park", "Huilongguan"): 17,  
    ("Tsinghua University", "Huilongguan"): 10  
}  
```
得到的最短的路径为："Huilongguan" -> "National Stadium" -> "Tsinghua University" -> "Huilongguan", 最短距离为 28

## 思路

1. 做一个上行graph, 一个下行graph,  下行graph要反着做, 这样才能用 Dijkstra
注意, 虽然是一个上行graph, 下行graph, 但是下行的graph需要反方向做, 这样做才可以搜索.  
上行graph之中的所有距离是起点到各个点之中的上行距离. **而下行反着做, 是所有其他点到起点的最短距离. 之所以下行需要反向构建, 因为最终会回到原点**, 所以, 在第三步要用原点的key进行搜索.  
**这一步是整道题的关键也是难点**, 想通了这一步之后这道题就没什么难度了.这个实在是很难用语言表示清楚, 建议画个图, 看图比较直观,思考会快一些.  这题挺难的, 但想10分钟是能理解的.  
2. 用Dijkstra求两个graph的最短路径. 因为题目中给定了起点是回龙观. 所以我们的dijkstra的结果是回龙观到所有其他所有地点的上行距离和下行距离.  
3. 把所有上行和下行的最短路径相加, 找出其中最小的和.

## 上行Graph

上行Graph 之中只保留上行路径

注意: 清华大学是一个孤岛, 上行无法到达. 而我们的djkstra算法会先预设每个点的距离是无穷大, 然后算最小值, 如果直接使用dijkstra的结果的话, 我们的清华大学的距离将变成无穷大, 伴随就会出现overflow, 而导致错误结果.  可以再djkstra之中的最后把所有无穷大的value消掉, 也可以在main方法之中用一个if轻松处理. 
      
![](https://lh3.googleusercontent.com/pw/ACtC-3f0ruhb6wmk4DVZxYFojEAJIljTklILPzzhFGVCdUpwweRtKluvDFBu1jXqYWV7lHmXhfuo1QVJuAPqeRezQoMA_2eDR6sQKQcWuHQolor8EZEyqN-udxCc-4nRd3n8JERZ7yJ-WBnhHZnzSMGLu09d=w601-h578-no?authuser=0)

## 下行Graph

下行Graph之中只保留下行路径

![](https://lh3.googleusercontent.com/pw/ACtC-3dtrLwphOr41sstEzWWrs4a-KQGDRzAdIgmnn6ZORoBcpnjrglcwjOHgW1Al_13GzgHF-rOSfDehKAgNDeR0Wj7c5rnUQ3gqNZcJdg7hEaTyHMPNdHXTPPZdqyua8m4RaBW6bnvpzUe4KnFRtk57Y7O=w620-h399-no?authuser=0)

## 求Djikstra

Djikstra的球法都是套路, 而且是以前重复的代码, 没什么可解释也没什么可理解的, 背就是了. 

## 相加

从Djistra我们得到上行最小和下行最小. 到同一个点的上行最小+下行最小必然得到全程最小. 故, 只要traverse所有点, 把上行下行的最短距离相加, 就可得最小值. 

## 最后

难点主要在于对Graph的理解和构建. 尤其是下行Graph要反方向构建. 理解了这个这道题其实就解了. 关于更多思路和代码注释看一下源码吧. 基本上都是我一个字一个字敲出来的, 写的很详尽了. 