---
title: Leetcode 456 我能赢么
date: 2022-05-22
lastmod: 2023-03-03
type: blog
series:
- 
tags:
- Leetcode
- DFS
- 状态压缩
description:
draft: false
cover:
    image: 
    caption: 
    alt: 
    relative: false
---

> 题目链接：[https://leetcode.cn/problems/can-i-win/](https://leetcode.cn/problems/can-i-win/)

## 思路

对于任一玩家，有两种情况：

1. 在当前可选集合中选择`i`，选择后满足大于等于`desiredTotal`，则该玩家赢。
2. 不满足 **1.** 的情况下，需要判断是否存在这样的`i`，使得另一玩家无法获胜（通过相同函数判断）。如果存在，则该玩家获胜（只要这一情况下选`i`就行）；否则，该玩家无法获胜（不论选任意`i`，另一玩家都能获胜）。

搜索的状态可以包括 **当前可选集合** 和 **当前还需累积和**，由于存在重复搜索的情况，使用[[记忆化搜索]]可以节省时间。对于 **当前可选集合** 可以使用[[状态压缩]]的方式表示。

注意到，两玩家每次必选一个整数，因此两人的搜索状态必然不同（可选集合的大小一奇一偶），无需额外区分。

## 代码

```C++
class Solution {
public:
    int maxN;
    bool mem[1<<20] = {0};  // 题目给的最大可选整数为20个，状态最大是20个1，即2^20-1
                            // 2^20大小数组够用
    bool dfs(unsigned int x, int tot) {
        if(mem[x]) return true;
        for(int i=0; i<maxN; i++) {
            if(((x>>i)&1)==0) {     // 右移判断第i个整数是否可选
                if(i+1>=tot) {      // 第i整数对应值是i+1
                    mem[x] = true;
                    return true;
                }
                if(!dfs(x|(1<<i),tot-(i+1))) {
                    mem[x] = true;
                    return true;
                }
            }
        }
        mem[x] = false;
        return false;
    }
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        maxN = maxChoosableInteger;
        // 判断给出整数总和小于所需累计和
        if((1+maxN)*maxN/2<desiredTotal) {  
            return false;
        }
        return dfs(0, desiredTotal);
    }
};
```
