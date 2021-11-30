---
layout: post 
title: LinkedList
date: 2021-11-29
author: Shadow Song
tags: [上课记录, LinkedList]
toc: true
comments: true
---

## DummyHead

LinkedList 分为三种问题: 

- 换头问题
- 138题
- LRU 和 LFU


```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        
        ListNode dummyHead = new ListNode(-1);
        ListNode prev = dummyHead;
                   
        return dummyHead.next;
    }
}
```

## LC24

- Iteration

```java
class Solution {
    public ListNode swapPairs(ListNode head) {

        // Dummy node acts as the prevNode for the head node
        // of the list and hence stores pointer to the head node.
        ListNode dummy = new ListNode(-1);
        dummy.next = head;

        ListNode prevNode = dummy;

        while ((head != null) && (head.next != null)) {

            // Nodes to be swapped
            ListNode firstNode = head;
            ListNode secondNode = head.next;

            // Swapping
            prevNode.next = secondNode;
            firstNode.next = secondNode.next;
            secondNode.next = firstNode;

            // Reinitializing the head and prevNode for next swap
            prevNode = firstNode;
            head = prevNode.next; // jump
        }

        // Return the new head node.
        return dummy.next;
    }
}
```

- Recursion

```java
    public ListNode swapPairs(ListNode head) {

        // If the list has no node or has only one node left.
        if ((head == null) || (head.next == null)) {
            return head;
        }

        // Nodes to be swapped
        ListNode firstNode = head;
        ListNode secondNode = head.next;

        // Swapping
        firstNode.next  = swapPairs(secondNode.next);
        secondNode.next = firstNode;

        // Now the head is the second node
        return secondNode;
    }
}
```

## LC148

找中点 + Merge Sort + 合并两个List.  一道题考三道题. 建议可以用来刷Merge Sort. 

## LC138

很特别的一道 HashMap. 

```java
class Solution {
    HashMap<Node, Node> visitedHash = new HashMap<Node, Node>();
    public Node copyRandomList(Node head) {
        
        if(head == null)
            return null;
        
        Node dummyHead = new Node(-1);
        dummyHead.next = head;
        
        //Traverse 两次
        
        // 1. 第一次把每个旧head放进hashmap
        while(head!=null)
        {
            visitedHash.put(head, new Node(head.val));
            head = head.next;
        }
        
        head = dummyHead.next;
        dummyHead = new Node(-1);
        Node output = dummyHead; 
                
        //2. 第二次用hashmap之中的新copy复制random和next. 
        while(head!=null)
        {
            output.next = visitedHash.get(head);

            if(head.random!=null){
                output.next.random = visitedHash.get(head.random);

            }
            output = output.next;
            head = head.next;
        }
        
        return dummyHead.next;
    }
}
```

## Double LinkedList

```java
class DLList{
    Node head, tail;  // MRU,  LRU
    int size;
    public DLList(){
        head = new Node();
        tail = new Node();
        head.next = tail;
        tail.prev = head;
    }
    public void add(Node node){
        node.next = head.next;
        node.prev = head;
        
        head.next.prev = node;
        head.next = node;
        size ++;
    }
    public void remove(Node node){
        node.next.prev = node.prev;
        node.prev.next = node.next;
        size --;
    }
    public void moveToHead(Node node){
        this.remove(node);
        this.add(node);
    }
    
    public Node removeLast(){
        if(size == 0) return null;
        Node node = tail.prev;
        remove(node);
        return node;
    }
}

class Node{
    Node prev, next;
    int key, val;
    
    public Node(int key, int val){
        this.key = key;
        this.val = val;
    }
    public Node(){}
}
```


## LC146_LRU

- **美国常考**

## 总结

- 遍历链表的时候，新创建一个头节点，以方便返回. 链表一般要开一个新节点以便回归头节点
- 倒着遍历或者从某位数第n个元素的时候, 用快慢指针
- **双向链表（LRU）**
- Null和走到末尾. 注意查next是否为空.   edge case. 
- 遍历的过程中用pre来记录前一个遍历的节点
- **链表 vs Array**：增加：链表插入的时候不用后移元素，删：链表删除不用后移元素，查找时间复杂度O(n)

## 作业

- 206, 876, 148, **138, 146**
- 写一下Python 的 double LinkedList 构建
- 剑指里链表都可以做了. 
