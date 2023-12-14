# 从一维差分到二维差分

## 前置知识

### 差分数组

关于一维差分，请参考【算法小课堂】差分数组。

### 二维前缀和

请参考【图解】二维前缀和。

## 思路

由于邮票可以互相重叠，贪心地想，能放邮票就放邮票。遍历所有能放邮票的位置去放邮票。注意邮票不能覆盖被占据的格子，也不能出界。放邮票的同时，记录每个空格子被多少张邮票覆盖。如果存在一个空格子没被邮票覆盖，则返回 `false`，否则返回 `true`。

## 细节

怎么快速判断一个矩形区域可以放邮票？求出 `grid` 的二维前缀和，从而 O(1) 地求出任意矩形区域的元素和。如果一个矩形区域的元素和等于 0，就表示该矩形区域的所有格子都是 0。

假设用一个二维计数矩阵 `cnt` 记录每个空格子被多少张邮票覆盖，那么放邮票时，就需要把 `cnt` 的一个矩形区域都加一。怎么快速实现？可以用二维差分矩阵 `d` 来代替 `cnt`。矩形区域都加一的操作，转变成 O(1) 地对 `d` 中四个位置的更新操作。

最后从二维差分矩阵 `d` 还原出二维计数矩阵 `cnt`。类似对一维差分数组求前缀和得到原数组，我们需要对二维差分矩阵求二维前缀和。遍历 `cnt`，如果存在一个空格子的计数值为 0，就表明该空格子没有被邮票覆盖，返回 `false`，否则返回 `true`。代码实现时，可以直接在 `d` 数组上原地计算出 `cnt`。

## 代码实现

```python
class Solution:
    def possibleToStamp(self, grid: List[List[int]], stampHeight: int, stampWidth: int) -> bool:
        m, n = len(grid), len(grid[0])

        # 1. 计算 grid 的二维前缀和
        s = [[0] * (n + 1) for _ in range(m + 1)]
        for i, row in enumerate(grid):
            for j, v in enumerate(row):
                s[i + 1][j + 1] = s[i + 1][j] + s[i][j + 1] - s[i][j] + v

        # 2. 计算二维差分
        d = [[0] * (n + 2) for _ in range(m + 2)]
        for i2 in range(stampHeight, m + 1):
            for j2 in range(stampWidth, n + 1):
                i1 = i2 - stampHeight + 1
                j1 = j2 - stampWidth + 1
                if s[i2][j2] - s[i2][j1 - 1] - s[i1 - 1][j2] + s[i1 - 1][j1 - 1] == 0:
                    d[i1][j1] += 1
                    d[i1][j2 + 1] -= 1
                    d[i2 + 1][j1] -= 1
                    d[i2 + 1][j2 + 1] += 1

        # 3. 还原二维差分矩阵对
        for i, row in enumerate(grid):
            for j, v in enumerate(row):
                d[i + 1][j + 1] += d[i + 1][j] + d[i][j + 1] - d[i][j]
                if v == 0 and d[i + 1][j + 1] == 0:
                    return False
```
## 复杂度分析
  时间复杂度：O(mn)\mathcal{O}(mn)O(mn)，其中 mmm 和 nnn 分别为 grid\textit{grid}grid 的行数和列数。
  空间复杂度：O(mn)\mathcal{O}(mn)O(mn)。


  
