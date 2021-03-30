---
layout: post
title:  "LeetCode 879 Profitable Schemes"
date:   2021-03-25
categories: 算法
tags: [算法]
---

There is a group of n members, and a list of various crimes they could commit. The ith crime generates a profit[i] and requires group[i] members to participate in it. If a member participates in one crime, that member can't participate in another crime.
Let's call a profitable scheme any subset of these crimes that generates at least minProfit profit, and the total number of members participating in that subset of crimes is at most n.
Return the number of schemes that can be chosen. Since the answer may be very large, return it modulo 109 + 7.

> Example 1:

**Input:** n = 5, minProfit = 3, group = [2,2], profit = [2,3]

**Output:**  2

**Explanation:**To make a profit of at least 3, the group could either commit crimes 0 and 1, or just crime 1. In total, there are 2 schemes.

> Example 2：

**Input:** n = 10, minProfit = 5, group = [2,3,5], profit = [6,7,8]

**Output:** 7

**Explanation:** To make a profit of at least 5, the group could commit any crimes, as long as they commit one. There are 7 possible schemes: (0), (1), (2), (0,1), (0,2), (1,2), and (0,1,2).

> Constraints:

1 <= n <= 100

0 <= minProfit <= 100

1 <= group.length <= 100

1 <= group[i] <= 100

profit.length == group.length

0 <= profit[i] <= 100

```java
public int profitableSchemes(int n, int minProfit, int[] group, int[] profit) {
        int mod = (int) 1e9 + 7;
        int N = profit.length;  //N项任务

        int dp[][][] = new int[N + 1][n + 1][minProfit + 1];  //dp[i][j][k] present execute pre i task cost j person can profit k

        for (int i = 0; i <= N; i++) {
            for (int j = 0; j <= n; j++) {
                dp[i][j][0] = 1;
            }
        }
        for (int i = 1; i <= N; i++) {
            int g = group[i - 1];
            int p = profit[i - 1];
            //三维dp，此处有问题。如果j < g 则不会进入，那么当不做第i件任务，也不会被更新
            //for (int j = n; j >= g; j--) {
            for (int j = 0; j <= n; j++) {
                for (int k = minProfit; k >= 0; k--) {
                    //第i件任务
                    //做 dp[i - 1][j - g][max(k - p， 0)]方案
                    //不做 dp[i - 1][j][max(k, 0)]方案
                    if (j < g) {
                        dp[i][j][k] = dp[i - 1][j][k];
                    } else {
                        dp[i][j][k] = dp[i - 1][j][k] + dp[i - 1][j - g][Math.max(k - p, 0)];
                    }
                    dp[i][j][k] %= mod;
                }
            }
        }
        return dp[N][n][minProfit];


        //优化空间复杂度O(n*minProfit)
//        int dp[][] = new int[n + 1][minProfit + 1];  //dp[i][j] present cost i person can profit j
//        for (int i = 0; i <= n; i++) {
//            dp[i][0] = 1;
//        }
//
//
//        for (int i = 0; i < N; i++) {
//            int g = group[i];
//            int p = profit[i];
//            for (int j = n; j >= g; j--) {
//                for (int k = minProfit; k >= 0; k--) {
//                    //第i件任务
//                    //做 dp[j - g][max(k - p， 0)]方案
//                    //不做 dp[j][max(k, 0)]方案
//                    dp[j][k] += dp[j - g][Math.max(k - p, 0)];
//                    dp[j][k] %= mod;
//                }
//            }
//        }
//        return dp[n][minProfit];
    }
```
## 题解
**dp\[i\]\[j\]\[k\]** 表示前i个任务消耗j个人获得k的利润的方案数量。

对第**i**个任务讨论，有两种状态   选择做还是选择不做

* do it：那么就是之前使用了**j-group\[i\]** 个人，获得利润 **k-profile\[i\]** 的方案数  就有 **dp\[i-1\]\[j-group[i]\]\[k-profit[i]\]** 种方案
* not do it: 那么就是之前使用了**j**个人，获得利润**k**的方案数 那么就有 **dp\[i-1\]\[j\]\[k\]** 种方案

那么总的方案个数就是有 **dp\[i\]\[j\]\[k\] = dp\[i-1\]\[j-group[i]\]\[k-profit[i]\] +  dp\[i-1\]\[j\]\[k\]**