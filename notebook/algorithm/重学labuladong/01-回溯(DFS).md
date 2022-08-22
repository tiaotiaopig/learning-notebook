# 回溯（DFS）

> 主要是解决排列组合和子集问题，通过枚举的方法，不漏不重
>
> 采用DFS深度优先遍历，主要是基于递归实现
>
> 回溯法和DFS非常类似，**回溯算法是在遍历「树枝」，DFS 算法是在遍历「节点」**

## 模板

解决一个回溯问题，实际上就是一个决策树的遍历过程，站在回溯树的一个节点上，你只需要思考 3 个问题：

1. 路径：也就是已经做出的选择。

2. 选择列表：也就是你当前可以做的选择。

3. 结束条件：也就是到达决策树底层，无法再做选择的条件。

```java
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    // 站在决策节点上
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

### 全排列

```java
private List<List<Integer>> res;

public List<List<Integer>> permute(int[] nums) {
    res = new ArrayList<>();
    backtrack(nums, new boolean[nums.length], new ArrayList<>());
    return res;
}

public void backtrack(int[] nums, boolean[] used, List<Integer> selected) {
    /**
        * nums 是选择列表，有时候还需要配置一个遍历索引，指示当前位置
        * selected 是路径
         */
    int n = nums.length;
    if (selected.size() == n) {
        res.add(new ArrayList<>(selected));
        return;
    }
    for (int i = 0; i < n; i++) {
        if (used[i]) continue;
        // 做选择
        used[i] = true;
        selected.add(nums[i]);
        // 走进新分支
        backtrack(nums, used, selected);
        // 新分支走完回来，撤销选择
        used[i] = false;
        selected.remove(selected.size() - 1);
    }
}
```

## 八皇后

> 回溯法核心没有变，主要是如何检测冲突。

```java
private List<List<String>> solution;

public List<List<String>> solveNQueens(int n) {
    solution = new ArrayList<>();
    char[][] board = new char[n][n];
    for (int i = 0; i < n; i++) Arrays.fill(board[i], '.');
    backtrack(0, board);
    return solution;
}

public void backtrack(int row, char[][] board) {
    int n = board.length;
    if (row == n) {
        solution.add(res(board));
        return;
    }
    for (int col = 0; col < n; col++) {
        if (!valid(row, col, board)) continue;
        board[row][col] = 'Q';
        backtrack(row + 1, board);
        board[row][col] = '.';
    }
}

public boolean valid(int row, int col, char[][] board) {
    int left = col - 1, right = col + 1;
    for (int i = row - 1; i >= 0; i--) {
        // 检测正上方是否冲突
        if (board[i][col] == 'Q') return false;
        // 检测左上方是否冲突
        if (left >= 0 && board[i][left--] == 'Q') return false;
        // 检测右上方是否冲突
        if (right < board.length && board[i][right++] == 'Q') return false;
    }
    return true;
}

public List<String> res(char[][] board) {
    int n = board.length;
    List<String> list =  new ArrayList<>();
    for(int i = 0; i < n; i++) list.add(new String(board[i]));
    return list;
}
```

