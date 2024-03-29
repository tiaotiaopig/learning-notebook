# 回溯法

## 概述

> ​		回溯算法实际上一个类似枚举的搜索尝试过程，主要是在搜索尝试过程中寻找问题的解，当发现已不满足求解条件时，就“回溯”返回，尝试别的路径。回溯法是一种选优搜索法，按选优条件向前搜索，以达到目标。但当探索到某一步时，发现原先选择并不优或达不到目标，就退回一步重新选择，这种走不通就退回再走的技术为回溯法，而满足回溯条件的某个状态的点称为“回溯点”。
>
> ​		回溯算法其实就是一个不断探索尝试的过程，探索成功了也就成功了，探索失败了就往后退一步，继续尝试其他可能，这种特性非常适合使用递归来实现，所以通常我们都是使用递归 + 回溯

## 算法模板

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径) # 深拷贝
        return

    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

> 上面的回溯算法中，每次进入递归函数，可以理解为进行一次决策。在遍历过程中使用的是 for 循环，说明在该次决策中，有多种选择，每一个可能的选择，我们都要进行尝试，尝试完成后，撤销选择，以便 for 中进行下一次选择
>
> 上述回溯过程，实现的是全排列

~~~java
public void backtrack (char[] str, int index, String t, StringBuilder sb) {
        if (sb.length() == t.length()) {
            if (sb.toString().equals(t)) count++;
            return;
        }
        if (index == str.length) return;
        // 使用当前字母
        sb.append(str[index]);
        backtrack(str, index + 1, t, sb);
        // 不使用当前字母
        sb.deleteCharAt(sb.length() - 1);
        backtrack(str, index + 1, t, sb);
    }
~~~

> 如果使用回溯法列举子序列（不是子串），我们就不能使用 for 循环了，因为这里每次决策，我们只有两种选择，选 or 不选
>
> 因此，我们只需要将 for 改掉就可以了，我们使用 index 控制当前遍历位置

## 两种写法

> 一种写法，面临决策 选 或者 不选，使用一个索引变量标识当前所在位置，这个思路更加清晰
>
> 另一种写法，`for` 循环，这个更加通用，也可以表示选 或者 不选
>
> 下面以39. 组合总和为例

```java
public List<List<Integer>> combinationSum(int[] candidates, int target) {
    List<List<Integer>> res = new ArrayList<>();
    backtrack(candidates, 0, target, new ArrayList<>(), res);
    return res;
}

public void backtrack(int[] candidates, int index, int target, List<Integer> selected, List<List<Integer>> res) {
    if (index == candidates.length || target <= 0) {
        if (target == 0) res.add(new ArrayList<>(selected));
        return ;
    }
    // 不选（跳过当前元素），index + 1
    backtrack(candidates, index + 1, target, selected, res);
    // 选择当前元素， index
    selected.add(candidates[index]);
    // 可以重复使用，还是 index
    backtrack(candidates, index, target - candidates[index], selected, res);
    selected.remove(selected.size() - 1);
}

public void backtrack(int[] candidates, int index, int target, List<Integer> selected, List<List<Integer>> res) {
    if (index == candidates.length || target <= 0) {
        if (target == 0) res.add(new ArrayList<>(selected));
        return ;
    }
    // 循环从index开始，保证结果不重复
    for (int i = index; i < candidates.length; i++) {
        // 选择 i
        selected.add(candidates[i]);
        backtrack(candidates, i, target - candidates[i], selected, res);
        selected.remove(selected.size() - 1);
        // 进入下一轮循环，就是不选 i
    }
}
```

 

## 说明

> 回溯法就是我们常说的深度优先遍历，本质上就是一种暴力穷举法，按照规律进行列举，防止有所遗漏
>
> 解决一个回溯问题，实际上就是一个决策树的遍历过程
>
> 需要考虑以下三个问题：
>
> 1. 路径：也就是已经做出的选择
> 2. 选择列表：当前可以做的选择
> 3. 结束条件：到达决策树底层，无需在做选择

## 两种写法

> 一种就是使用`for` 循环的标准模板
>
> 另一种就是 选 或者 不选 ，使用一个index 表示当前待选元素
>
> 本质上二者是一致的，`for` 循环的逻辑中，就包含了选 或者 不选

## 典型题目

> 1. 46  全排列
> 2. 51 N皇后
> 3. 131 分割回文串

1. 46 全排列

   ```java
       public List<List<Integer>> permute(int[] nums) {
           List<List<Integer>> res = new ArrayList<>();
           backtrack(nums, new ArrayList<>(), res);
           return res;
       }
   
       public void backtrack(int[] nums, List<Integer> selected, List<List<Integer>> res) {
           if (selected.size() == nums.length) {
               res.add(new ArrayList<>(selected));
               return ;
           }
           for (int i = 0; i < nums.length; i++) {
               if (!selected.contains(nums[i])) {
                   selected.add(nums[i]);
                   backtrack(nums, selected, res);
                   selected.remove(selected.size() - 1);
               }
           }
       }
   ```
> 4. 39 组合总和
> 5. 40 组合总和II
> 6. 47 全排列II
> 7. 78 子集
> 8. 90 子集II
