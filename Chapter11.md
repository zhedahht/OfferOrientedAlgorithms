# 第十一章：二分查找
## 面试题68：查找插入位置
### 题目
输入一个排序的整数数组nums和一个目标值t，如果nums中包含t，返回t在数组中的下标；如果nums中不包含t，则返回如果将t添加到nums里时t在nums中的下标。假设数组中的没有相同的数字。例如，输入数组nums为[1, 3, 6, 8]，如果目标值t为3，则输出1；如果t为5，则返回2。

### 参考代码
``` java
public int searchInsert(int[] nums, int target) {
    int left = 0;
    int right = nums.length - 1;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (nums[mid] >= target) {
            if (mid == 0 || nums[mid - 1] < target) {
                return mid;
            }

            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }

    return nums.length;
}
```

## 面试题69：山峰数组的顶部
### 题目
在一个长度大于或等于3的数组里，任意相邻的两个数都不相等。该数组的前若干个数字是递增的，之后的数字是递减的，因此它的值看起来像一座山峰。请找出山峰顶部即数组中最大值的位置。例如，在数组[1, 3, 5, 4, 2]中，最大值是5，输出它在数组中的下标2。

### 参考代码
``` java
public int peakIndexInMountainArray(int[] nums) {
    int left = 1;
    int right = nums.length - 2;
    while (left <= right) {
        int mid = (left + right) / 2;
        if (nums[mid] > nums[mid + 1] && nums[mid] > nums[mid - 1]) {
            return mid;
        }

        if (nums[mid] > nums[mid - 1]) {
            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return -1;
}
```

## 面试题70：排序数组中只出现一次的数字
### 题目
在一个排序的数组中，除了一个数字只出现一次之外其他数字数字都出现了两次，请找出这个唯一只出现一次的数字。例如，在数组[1, 1, 2, 2, 3, 4, 4, 5, 5]中，数字3只出现一次。

### 参考代码
``` java
public int singleNonDuplicate(int[] nums) {
    int left = 0;
    int right = nums.length / 2;
    while (left <= right) {
        int mid = (left + right) / 2;
        int i = mid * 2;
        if (i < nums.length - 1 && nums[i] != nums[i + 1]) {
            if (mid == 0 || nums[i - 2] == nums[i - 1]) {
                return nums[i];
            }

            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }

    return nums[nums.length - 1];
}
```

## 面试题71：按权重生成随机数
### 题目
输入一个正整数数组w，数组中的每个数字w[i]表示下标i的权重，请实现一个函数pickIndex根据权重比例随机选择一个下标。例如，如果权重数组w为[1, 2, 3, 4]，这pickIndex将有10%的概率选择0、20%的概率选择1、30%的概率选择2、40%的概率选择3。

### 参考代码
``` java
class Solution {
    private int[] sums;
    private int total;
    
    public Solution(int[] w) {
        sums = new int[w.length];
        for (int i = 0; i < w.length; ++i) {
            total += w[i];
            sums[i] = total;
        }
    }
    
    public int pickIndex() {
        Random random = new Random();
        int p = random.nextInt(total);
        int left = 0;
        int right = sums.length;
        while (left <= right) {
            int mid = (left + right) / 2;
            if (sums[mid] > p) {
                if (mid == 0 || (sums[mid - 1] <= p)) {
                    return mid;
                }
                
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        
        return -1;
    }
}
```

## 面试题72：求平方根
### 题目
输入一个非负整数，请计算它的平方根。正数的平方根有两个，只输出其中正数平方根。如果平方根不是整数，只需要输出它的整数部分。例如，如果输入4则输出2；如果输入18则输出4。

### 参考代码
``` java
public int mySqrt(int n) {
    int left = 1;
    int right = n;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        if (mid <= n / mid) {
            if ((mid + 1) > n / (mid + 1)) {
                return mid;
            }

            left = mid + 1;
        } else {
            right = mid - 1;
        }
    }

    return 0;
}
```

## 面试题73：狒狒吃香蕉
### 题目
狒狒很喜欢吃香蕉。一天它发现了n堆香蕉，第i堆有piles[i]个香蕉。门卫刚好走开了要H小时后才会回来。狒狒吃香蕉喜欢细嚼慢咽，但又想在门卫回来之前吃完所有的香蕉。请问狒狒每小时至少吃多少根香蕉？如果狒狒决定每小时吃k根香蕉，而它在吃的某一堆剩余的香蕉的数目少于k，那么它只会将这一堆的香蕉吃完，下一个小时才会开始吃另一堆的香蕉。

### 参考代码
``` java
public int minEatingSpeed(int[] piles, int H) {
    int max = Integer.MIN_VALUE;
    for (int pile : piles) {
        max = Math.max(max, pile);
    }

    int left = 1;
    int right = max;
    while (left <= right) {
        int mid = left + (right - left) / 2;
        int hours = getHours(piles, mid);
        if (hours <= H) {
            if (mid == 1 || getHours(piles, mid - 1) > H) {
                return mid;
            }

            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }

    return -1;
}

private int getHours(int[] piles, int speed) {
    int hours = 0;
    for (int pile : piles) {
        hours += (pile + speed - 1) / speed;
    }

    return hours;
}
```
