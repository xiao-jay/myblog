---
title: 剑指 Offer 12. 矩阵中的路径
excerpt: 所在模块：array
tags: [golang,array]
categories: 力扣刷题
banner_img: /img/壁纸.jpg
---

### <font size=6px>[剑指 Offer 12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)</font>

#### 题目描述：

给定一个 m x n 二维字符网格 board 和一个字符串单词 word 。如果 word 存在于网格中，返回 true ；否则，返回 false 。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

 

示例 1：


输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
示例 2：


输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "SEE"
输出：true
示例 3：


输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCB"
输出：false


提示：

m == board.length
n = board[i].length
1 <= m, n <= 6
1 <= word.length <= 15
board 和 word 仅由大小写英文字母组成


进阶：你可以使用搜索剪枝的技术来优化解决方案，使其在 board 更大的情况下可以更快解决问题？

#### 思路:

经典dfs题，visit访问时要置1，最后访问完后重新置0

#### 代码:

```golang
func exist(board [][]byte, word string) bool {
    m,n := len(board), len(board[0]) 
    visit := make([][]bool, len(board))
    valid := false
    move := []int{1,0,0,1,0,-1,-1,0}
    for i:= 0; i<len(board); i++{
        visit[i] = make([]bool, len(board[i]))
    }
    var dfs func(num, x,y int)
    dfs = func(count,x,y int){
        if count == len(word){
            valid = true
        }
        if valid{
            return 
        }
        visit[x][y] = true
        for i:=0; i<4;i++{
            new_x := x+move[i*2]
            new_y := y+move[i*2 + 1]
            if new_x >= 0 && new_x < m && new_y >=0 && new_y <n && visit[new_x][new_y] == false && board[new_x][new_y ] == word[count]{
                
                dfs(count+1, new_x,new_y)
            }
        }
        visit[x][y] = false
    }
    
    for i := 0;i<len(board); i++{
        for j := 0; j<len(board[i]); j++{
            count := 1
            if board[i][j] == word[0] && !valid{
                
                dfs(count, i, j)
            }
            
        } 
    }
    return valid
}
```

#### 代码效率：

<p class="note note-primary"; style="font-size:22px">
   执行用时：92 ms, 在所有 Go 提交中击败了59.42%的用户<br>
   内存消耗：1.9 MB, 在所有 Go 提交中击败了91.92%的用户
</p>


