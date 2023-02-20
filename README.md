# 剑指Offer
## 数据结构与算法名企面试题精讲
### 作者：何海涛
---
#### 第1章：[整数](./Chapter01.md)
#### 第2章：[数组](./Chapter02.md)
#### 第3章：[字符串](./Chapter03.md)
#### 第4章：[链表](./Chapter04.md)
#### 第5章：[哈希表](./Chapter05.md)
#### 第6章：[栈](./Chapter06.md)
#### 第7章：[队列](./Chapter07.md)
#### 第8章：[树](./Chapter08.md)
#### 第9章：[堆](./Chapter09.md)
#### 第10章：[前缀树](./Chapter10.md)
#### 第11章：[二分查找](./Chapter11.md)
#### 第12章：[排序](./Chapter12.md)
#### 第13章：[回溯法](./Chapter13.md)
#### 第14章：[动态规划](./Chapter14.md)
#### 第15章：[图](./Chapter15.md)
---
## 购买网址
+ 京东网：https://item.jd.com/13371972.html
+ 当当网：http://product.dangdang.com/29280950.html
---
## 勘误
### 第一版
+ 第2页，面试题1的分析的第1段的第2行：求(2^31-1)/1，减1的操作将执行2^32-1次。应该改为：求(2^31-1)/1，减1的操作将执行2^31-1次。
+ 第19页，面试题8的分析的第4段的第5-6行：再把指针P2向右移动一步指向数字6。应该改为：再把指针P2向右移动一步指向数字4。
+ 第23页，面试题10的分析的第5段的最后一句话：那么数组中从第i+1个数字开始到第j个数字结束的子数组之和为k。应该改为：那么数组中从第j+1个数字开始到第i个数字结束的子数组之和为k。
+ 第27页，面试题13的图2.2下面的第二段的“该子矩阵的数字之和等于sums[r2][c2]+sums[r1-1][c2]-sums[r2][c1-1]+sums[r1-1][c1-1]”，应改为“该子矩阵的数字之和等于sums[r2][c2]-sums[r1-1][c2]-sums[r2][c1-1]+sums[r1-1][c1-1]”。
+ 第51页，面试题21的代码中的参数n改为k，即
``` java
public ListNode removeNthFromEnd(ListNode head, int n) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode front = head, back = dummy;
    for (int i = 0; i < n; i++) {
        front = front.next;
    }

    while (front != null) {
        front = front.next;
        back = back.next;
    }

    back.next = back.next.next;                    
    return dummy.next;
}
```
应该改为：
``` java
public ListNode removeNthFromEnd(ListNode head, int k) {
    ListNode dummy = new ListNode(0);
    dummy.next = head;

    ListNode front = head, back = dummy;
    for (int i = 0; i < k; i++) {
        front = front.next;
    }

    while (front != null) {
        front = front.next;
        back = back.next;
    }

    back.next = back.next.next;                    
    return dummy.next;
}
```
+ 第104页，第3行：宽是1（5-1-1=3）。应该改为：宽是3（5-1-1=3）。
+ 第112页，面试题42的题目描述
``` java
class RecentAverage {    
    public RecentCounter();
    public int ping(int t);
}
```
应该改为：
``` java
class RecentCounter {    
    public RecentCounter();
    public int ping(int t);
}
```
+ 第245页，面试题88“使用缓存的递归代码”参考代码中的minCostClimbingStairs函数
``` java
public int minCostClimbingStairs(int[] cost) {
    int len = cost.length;
    int[] dp = new int[len];
    helper(cost, len - 1, dp);
    return Math.min(dp[len - 2], dp[len - 1]);
}
```
应该改为：
``` java
public int minCostClimbingStairs(int[] cost) {
    int len = cost.length;
    int[] dp = new int[len];
    dp[0] = cost[0];
    helper(cost, len - 1, dp);
    return Math.min(dp[len - 2], dp[len - 1]);
}
```
增加了一行代码初始化dp[0]。
+ 第249页，面试题89的"分析确定状态转移方程"的第4段的第3行：因此f(1)=nums[0]。应该改为：因此f(0)=nums[0]。
+ 第256页，第2段的第二行“当i等于时”，应改为“当i等于0时”。
+ 第300页，面试题103的"分析确定状态转移方程"的第4行：再加上1枚标号为i-1的硬币。应该改为：再加上0枚标号为i-1的硬币。
