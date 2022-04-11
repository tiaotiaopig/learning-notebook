# KMP 算法

> 字符串的模式匹配算法，在一个很长的主串（**main**）中寻找一个指定的子串（**sub**）在主串中出现的位置，可以是第一次出现，也可以多次出现。阿里面试的时候遇到了，呜呜，还是不能有知识的漏洞呀，赶紧亡羊补牢吧

## 简单模式匹配

一个直接的想法：主串中逐个遍历，找到和sub匹配的就逐个匹配，两重for循环，时间复杂度**O(mn)**，空间复杂度**O(1)**

```java
public int strStr(String haystack, String needle) {
    char[] main = haystack.toCharArray(), sub = needle.toCharArray();
    int mLen = main.length, sLen = sub.length;
    for (int i = 0, j = 0; i <= mLen - sLen; i++, j = 0) {
        // 开始逐个匹配
        while (j < sLen && main[i + j] == sub[j]) j++;
        // 匹配成功
        if (j == sLen) return i;
    }
    return -1;
}
```

## KMP模式匹配

KMP算法的时间复杂度是**O(n)**，空间复杂度**O(m*N)**。m为子串长度，N为所有可能的字符数量

将子串（sub）的匹配过程抽象为一个有限状态机，匹配的过程，就是状态转移的过程

![img](https://s2.loli.net/2022/04/11/h9TEBKaZzCFe1cY.jpg)

如上图，每次遇到一个字符，状态都要进行转移:

​	匹配成功的时候，状态向前推进，**state + 1**

​	匹配失败，状态回退，回退到哪里呢，回退到 **state 0**，就是简单模式匹配了；优化就体现在这里了，回退状态要参考和**当前状态最长后缀的那个状态**面临这个字符选择时要转移的状态，如果没有，回退到 **state 0**，我们用一个二维数组将这个有限状态机给记录下来

```java
public int strStr(String haystack, String needle) {
    char[] txt = haystack.toCharArray(), pat = needle.toCharArray();
    int len1 = txt.length, len2 = pat.length;
    int[][] kmp = state(pat);
    for (int i = 0, curr = 0; i < len1; i++) {
        curr = kmp[curr][txt[i]];
        if (curr == len2) return i - len2 + 1;
    }
    return -1;
}

private int[][] state(char[] pat) {
    int len = pat.length;
    int[][] kmp = new int[len][256];
    // 当前状态后缀最长匹配的那个状态
    int preState = 0;
    // 匹配就是状态1，不匹配就是0
    kmp[0][pat[0]] = 1;
    for (int i = 1; i < len; i++) {
        // i 这种状态遇到各种字符如何转移，这里空间可以优化
        for (int j = 0; j < 256; j++) {
            // 匹配时，状态前进，不匹配，参考preState
            kmp[i][j] = pat[i] == j ? i + 1 : kmp[preState][j];
        }
        // 这里很关键呀，更新preState，为枚举 i + 1 这个状态做准备
        preState = kmp[preState][pat[i]];
    }
    return kmp;
}
```

