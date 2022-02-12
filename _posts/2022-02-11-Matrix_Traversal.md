---
layout: post 
title: Matrix Traversal
date: 2022-02-11
author: Shadow Song
tags: [上课记录, Matrix]
toc: true
comments: true
---

## DFS

- 200
	- [No Iteration](https://leetcode.com/problems/number-of-islands/discuss/1731734/Iterative-Solution-(using-DFS-Java)) and [python Iteration](https://leetcode.com/problems/number-of-islands/discuss/1758785/Python-DFS-solution)

```java
class Solution {
    int ROW;
    int COL;
    char[][] grid;
    private void dfs(int row, int col){
        if(!checkBoundary(row, col) || grid[row][col] == '0')
            return;
        
        grid[row][col] = '0';
        dfs(row+1, col);
        dfs(row-1, col);
        dfs(row, col+1);
        dfs(row, col-1);
    }
    
    private boolean checkBoundary(int row, int col){
        return row>=0 && col>=0 && row<ROW && col<COL;
    }
    
    public int numIslands(char[][] grid) {
        this.grid = grid;
        this.ROW = grid.length;
        this.COL = grid[0].length;
        int output = 0;
        for(int i=0; i<ROW; i++){
            for(int j=0; j<COL; j++){
                if(grid[i][j]=='1'){
                    dfs(i,j);
                    output ++;
                }
            }
        }
        return output;
    }
}
```

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:

        if not grid:
            return 0

        ROW, COL = len(grid), len(grid[0])

        def dfs(row, col):
            if checkBoundary(row, col):
                grid[row][col] = '0'

                dfs(row + 1, col)
                dfs(row - 1, col)
                dfs(row, col + 1)
                dfs(row, col - 1)
        
        def checkBoundary(row, col):
            return row>=0 and col>=0 and row<ROW and col<COL and grid[row][col] == '1'

        output = 0
        for row in range(ROW):
            for col in range(COL):
                if grid[row][col] == '1':
                    dfs(row, col)
                    output += 1
        return output
```

### Backtrack

- 79

```java
class Solution {
  private char[][] board;
  private int ROWS;
  private int COLS;

  public boolean exist(char[][] board, String word) {
    this.board = board;
    this.ROWS = board.length;
    this.COLS = board[0].length;

    for (int row = 0; row < this.ROWS; ++row)
      for (int col = 0; col < this.COLS; ++col)
        if (this.backtrack(row, col, word, 0))
          return true;
    return false;
  }

  protected boolean backtrack(int row, int col, String word, int index) {
    /* Step 1). check the bottom case. */
    if (index >= word.length())
      return true;

    /* Step 2). Check the boundaries. */
    if (row < 0 || row == this.ROWS || col < 0 || col == this.COLS
        || this.board[row][col] != word.charAt(index))
      return false;

    /* Step 3). explore the neighbors in DFS */
    boolean ret = false;
    // mark the path before the next exploration
    // 这里染色, 为的是不再从子类往parent回走. 
    this.board[row][col] = '#';

    int[] rowOffsets = {0, 1, 0, -1};
    int[] colOffsets = {1, 0, -1, 0};
    for (int d = 0; d < 4; ++d) {
      ret = this.backtrack(row + rowOffsets[d], col + colOffsets[d], word, index + 1);
      // 如果有一个true值直接断开, 后面就不用看了. 
      if (ret)
        break;
    }

    /* Step 4). clean up and return the result. */
    // 这一步是为了把颜色去掉. 就是把#改回来正常值. 
    this.board[row][col] = word.charAt(index);
    return ret;
  }
}
```

## BFS

- 994

## 难题

- 827, 用 695的图来讲. 


## 技巧

- DFS 模板
- 染色, 去色


## Problems

- Leetcode练习: 1730, 岛系题目, 490
- 剑指: 12(涉及回溯), 47涉及动态规划. 基本都涉及其他知识. 

## 其他

- Chess