---
layout: post
title:  "LeetCode 4 Median of Two Sorted Arrays"
date:   2021-03-25
categories: 算法
tags: [算法]
---

## Median of Two Sorted Arrays

Given two sorted arrays nums1 and nums2 of size m and n respectively, return the median of the two sorted arrays.

> Example 1:

**Input:** nums1 = [1,3], nums2 = [2]

**Output:** 2.00000

**Explanation:** merged array = [1,2,3] and median is 2.

> Example 2:

**Input:** nums1 = [1,2], nums2 = [3,4]

**Output:** 2.50000

**Explanation:** merged array = [1,2,3,4] and median is (2 + 3) / 2 = 2.5.

> Example 3:

**Input:** nums1 = [0,0], nums2 = [0,0]
**Output:** 0.00000

> Example 4:

**Input:** nums1 = [], nums2 = [1]
**Output:** 1.00000

> Example 5:

**Input:** nums1 = [2], nums2 = []
**Output:** 2.00000

> Constraints:

nums1.length == m
nums2.length == n
0 <= m <= 1000
0 <= n <= 1000
1 <= m + n <= 2000
-106 <= nums1[i], nums2[i] <= 106

## Solution

假设给定数组A、B，已排序。

1. 合并A、B，生成C，中位数就是C数组中间的数

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int[] nums;
    int m = nums1.length;
    int n = nums2.length;
    nums = new int[m + n];
    if (m == 0) {
        if (n % 2 == 0) {
            return (nums2[n / 2 - 1] + nums2[n / 2]) / 2.0;
        } else {

            return nums2[n / 2];
        }
    }
    if (n == 0) {
        if (m % 2 == 0) {
            return (nums1[m / 2 - 1] + nums1[m / 2]) / 2.0;
        } else {
            return nums1[m / 2];
        }
    }

    int count = 0;
    int i = 0, j = 0;
    while (count != (m + n)) {
        if (i == m) {
            while (j != n) {
                nums[count++] = nums2[j++];
            }
            break;
        }
        if (j == n) {
            while (i != m) {
                nums[count++] = nums1[i++];
            }
            break;
        }

        if (nums1[i] < nums2[j]) {
            nums[count++] = nums1[i++];
        } else {
            nums[count++] = nums2[j++];
        }
    }

    if (count % 2 == 0) {
        return (nums[count / 2 - 1] + nums[count / 2]) / 2.0;
    } else {
        return nums[count / 2];
    }
}
/**
* 时间复杂度：遍历全部数组 O(m+n)
* 空间复杂度：开辟了一个数组，保存合并后的两个数组 O(m+n)
*/
```

2. 无需合并，已知A、B，那么就知道了合成数组的总长度`len`，那么中位数的位置也就知道了。此时有两种情况

   * `len`为偶数，那么只需找到合成数组的第`len`/2  和 第`len`/2 + 1个元素的值的平均值
   * `len`为奇数，那么只需找到合成数组的第`len`/2 个元素值

   维护两个指针p, q，遍历A、B即可。分别从小到大遍历数组直到指定位置

   ```java
   public double findMedianSortedArrays(int[] A, int[] B) {
       int m = A.length;
       int n = B.length;
       int len = m + n;
       int p = -1, q = -1;
       int aStart = 0, bStart = 0;
       for (int i = 0; i <= len / 2; i++) {
           p = q;
           if (aStart < m && (bStart >= n || A[aStart] < B[bStart])) {
               q = A[aStart++];
           } else {
               p = B[bStart++];
           }
       }
       if ((len & 1) == 0)
           return (p + q) / 2.0;
       else
           return right;
   }
   /**
   * 时间复杂度：遍历全部数组 O(m+n)
   * 空间复杂度：开辟了一个数组，保存合并后的两个数组 O(1)
   */
   ```

   

3. 二分

   以上方法的时间复杂度都是o(m + n),如何将时间复杂度降低至O(log(m + n))呢？看到log条件反射二分法

   由上一个解法我们一直，求两个数组的中位数可以分解为找第`len`/2的元素或者第`len`/2  和 第`len`/2 + 1个元素。那么问题就变成了找第k个小的问题了。

   第k小问题 我们一般使用分治的思路(快排的思路)，此题也同样使用这个思路。

   ***analysis***

   我们可以比较 A[k/2-1]和 B[k/2−1]。由于 A[k/2−1] 和 B[k/2−1] 的前面分别有 A[0..k/2−2] 和 B[0..k/2−2]，即k/2−1 个元素，对于 A[k/2−1] 和 B[k/2−1] 中的较小值，最多只会有(k/2−1)+(k/2−1)≤k−2 个元素比它小，那么它就不能是第 k 小的数了。

   因此我们可以归纳出三种情况：

   * 如果 A[k/2−1]<B[k/2−1]，则比A[k/2−1] 小的数最多只有A 的前k/2−1 个数和 B 的前k/2−1 个数，即比 A[k/2−1] 小的数最多只有 k−2 个，因此A[k/2−1] 不可能是第 k 个数，A[0] 到 A[k/2−1] 也都不可能是第 k 个数，可以全部排除。

   * 如果 A[k/2−1]>B[k/2−1]，则可以排除B[0] 到B[k/2−1]。

   * 如果 A[k/2−1]=B[k/2−1]，可以归入第一种情况处理。

   可以看到，比较 A[k/2−1] 和 B[k/2−1] 之后，可以排除k/2 个不可能是第 k 小的数，查找范围缩小了一半。同时，我们将在排除后的新数组上继续进行二分查找，并且根据我们排除数的个数，减少 k 的值，这是因为我们排除的数都不大于第 k 小的数。

   有以下三种情况需要特殊处理：

   * 如果 A[k/2−1] 或者 B[k/2−1] 越界，那么我们可以选取对应数组中的最后一个元素。在这种情况下，我们必须根据**排除数的个数**减少 k 的值，而不能直接将 k 减去 k/2。

   * 如果一个数组为空，说明该数组中的所有元素都被排除，我们可以直接返回另一个数组中第 k 小的元素。

   * 如果 k=1(最小的数)，我们只要返回两个数组首元素的最小值即可。

   

用一个例子说明上述算法。假设两个有序数组如下：

```
A: 1 3 4 9
B: 1 2 3 4 5 6 7 8 9
```

两个有序数组的长度分别是 4 和 9，长度之和是 13，中位数是两个有序数组中的第 7 个元素，因此需要找到第 k=7 个元素。

比较两个有序数组中下标为 k/2−1=2 的数，即 A[2] 和 B[2]，如下面所示：

```
A: 1 3 4 9
       ↑
B: 1 2 3 4 5 6 7 8 9
       ↑
```

由于 A[2]>B[2]，因此排除 B[0] 到 B[2]，即数组 B 的下标偏移（offset）变为 3，同时更新 k 的值：k=k−k/2=4。

下一步寻找，比较两个有序数组中下标为 k/2−1=1 的数，即 A[1] 和 B[4]，如下面所示，其中方括号部分表示已经被排除的数。

```
A: 1 3 4 9
     ↑
B: [1 2 3] 4 5 6 7 8 9
             ↑
```

由于 A[1]<B[4]，因此排除 A[0] 到 A[1]，即数组A 的下标偏移变为 2，同时更新 kk 的值：k=k−k/2=2。

下一步寻找，比较两个有序数组中下标为 k/2−1=0 的数，即比较A[2] 和 B[3]，如下面所示，其中方括号部分表示已经被排除的数。

```
A: [1 3] 4 9
         ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```

由于A[2]=B[3]，根据之前的规则，排除A 中的元素，因此排除A[2]，即数组 A 的下标偏移变为 3，同时更新 kk 的值： k=k-k/2=1。

由于 k 的值变成 1，因此比较两个有序数组中的未排除下标范围内的第一个数，其中较小的数即为第 k 个数，由于A[3]>B[3]，因此第 k 个数是 B[3]=4。

```
A: [1 3 4] 9
           ↑
B: [1 2 3] 4 5 6 7 8 9
           ↑
```

```java
public class Solution {
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int n = nums1.length;
        int m = nums2.length;

        int len = n + m;

        if(len % 2 == 1) {
            int k = len / 2;
            double median = getKthElement(nums1, nums2, k + 1);
            return median;
        } else {
            int k1 = len / 2 - 1;
            int k2 = len / 2;

            double median = (getKthElement(nums1, nums2, k1 + 1) + getKthElement(nums1, nums2, k2 + 1)) / 2.0;
            return median;
        }
    }

    private double getKthElement(int[] nums1, int[] nums2, int k) {
        /* 主要思路：要找到第 k (k>1) 小的元素，那么就取 pivot1 = nums1[k/2-1] 和 pivot2 = nums2[k/2-1] 进行比较
         * 这里的 "/" 表示整除
         * nums1 中小于等于 pivot1 的元素有 nums1[0 .. k/2-2] 共计 k/2-1 个
         * nums2 中小于等于 pivot2 的元素有 nums2[0 .. k/2-2] 共计 k/2-1 个
         * 取 pivot = min(pivot1, pivot2)，两个数组中小于等于 pivot 的元素共计不会超过 (k/2-1) + (k/2-1) <= k-2 个
         * 这样 pivot 本身最大也只能是第 k-1 小的元素
         * 如果 pivot = pivot1，那么 nums1[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums1 数组
         * 如果 pivot = pivot2，那么 nums2[0 .. k/2-1] 都不可能是第 k 小的元素。把这些元素全部 "删除"，剩下的作为新的 nums2 数组
         * 由于我们 "删除" 了一些元素（这些元素都比第 k 小的元素要小），因此需要修改 k 的值，减去删除的数的个数
         */
        int n = nums1.length;
        int m = nums2.length;
        int index1 = 0, index2 = 0;
        while (true) {
            //边界
            if(index1 == n) {
                return nums2[index2 + k - 1];
            }
            if(index2 == m) {
                return nums1[index1 + k - 1];
            }
            if(k == 1) {
                return Math.min(nums1[index1], nums2[index2]);
            }
            // 正常情况
            int mid = k / 2;
            int newIndex1 = Math.min(index1 + mid, n) - 1;
            int newIndex2 = Math.min(index2 + mid, m) - 1;
            int pivot1 = nums1[newIndex1], pivot2 = nums2[newIndex2];
            if(pivot1 < pivot2) {
                k -= (newIndex1 - index1 + 1);
                index1 = newIndex1 + 1;
            } else {
                k -= (newIndex2 - index2 + 1);
                index2 = newIndex2 + 1;
            }
        }
    }
}
/**
* 时间复杂度：遍历全部数组 O(log(m+n))
* 空间复杂度：开辟了一个数组，保存合并后的两个数组 O(1)
*/
```
## TIP
此处非常感谢[LeetCode-Solution](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xun-zhao-liang-ge-you-xu-shu-zu-de-zhong-wei-s-114/)提供的第三种方法的文案