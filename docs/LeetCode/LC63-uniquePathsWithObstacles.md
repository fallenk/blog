---
title: LC63_uniquePathsWithObstacles
date: 2020-07-06 14:50:29
tags: [LeetCode]
---

# 题目

https://leetcode-cn.com/problems/unique-paths-ii/

# 解法

首先的思路： 递归+回溯;  m 与 n

```java
public int uniquePathsWithObstacles(int[][] obstacleGrid) {
        if (obstacleGrid == null || obstacleGrid.length == 0) {
            return 0;
        }
        int pathCount = dfs(0, 0, obstacleGrid);
        return pathCount;
    }
    /**
     * 计算是否是路径
     * @param i
     * @param j
     * @param obstacleGrid
     * @param pathCount
     */
    private int dfs(int i, int j, int[][] obstacleGrid) {
        int row = obstacleGrid.length;
        int col = obstacleGrid[0].length;
        if (i>=row||i<0 || j<0||j>=col || obstacleGrid[i][j] == 1) {
            return 0;
        }
        if((i==row-1) && (j==col-1)&&obstacleGrid[i][j] != 1) {
         
            return 1;
        }
        int downPath = 0;
        if (i+1 <row&&obstacleGrid[i+1][j] != 1) {
            downPath =  dfs(i+1, j, obstacleGrid);
        }
        int rightPath = 0;
        if (j+1 < col&&obstacleGrid[i][j+1] != 1) {
            rightPath =  dfs(i, j+1, obstacleGrid);
        }
        return downPath + rightPath;
    }
```

