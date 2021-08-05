# 第九章：堆
## 面试题59：数据流的第k大数值
### 题目
请设计一个类型KthLarges，它每次从一个数据流中读取一个数字，并得出数据流已经读取的数字中第k（k≥1）大的数值。该类型的构造函数有两个参数，一个是整数k，另一个是包含数据流中最开始数值的整数数组nums（假设数组nums的长度大于k）。该类型还有一个函数add用来添加数据流中的新数值并返回数据流中已经读取的数字的第k大数值。

例如，当k=3、nums为数组[4, 5, 8, 2]时，调用构造函数创建除类型KthLargest的实例之后，第一次调用add函数添加数字3，此时已经从数据流里读取了数值4、5、8、2和3，第3大的数值是4；第二次调用add函数添加数字5时，则返回第3大的数值5。

### 参考代码
``` java
class KthLargest {
    private PriorityQueue<Integer> minHeap;
    private int size;

    public KthLargest(int k, int[] nums) {
        size = k;
        minHeap = new PriorityQueue<>();
        for (int num : nums) {
            add(num);
        }
    }

    public int add(int val) {
        if (minHeap.size() < size) {
            minHeap.offer(val);
        } else if (val > minHeap.peek()) {
            minHeap.poll();
            minHeap.offer(val);
        }

        return minHeap.peek();
    }
}
```

## 面试题60：出现频率最高的k个数字
### 题目
请找出数组中出现频率最高的k个数字。例如当k等于2时输入数组[1, 2, 2, 1, 3, 1]，由于数字1出现3次，数字2出现2次，数字3出现1，那么出现频率最高的2个数字时1和2。

### 参考代码
``` java
public int[] topKFrequent(int[] nums, int k) {
    Map<Integer, Integer> numToCount = new HashMap<>();
    for (int num : nums) {
        numToCount.put(num, numToCount.getOrDefault(num, 0) + 1);
    }

    Queue<Map.Entry<Integer, Integer>> minHeap = new PriorityQueue<>(
        (e1, e2) -> e1.getValue() - e2.getValue());
    for (Map.Entry<Integer, Integer> entry : numToCount.entrySet()) {
        if (minHeap.size() < k) {
            minHeap.offer(entry);
        } else {
            if (entry.getValue() > minHeap.peek().getValue()) {
                minHeap.poll();
                minHeap.offer(entry);
            }
        }
    }

    int[] result = new int[k];
    for (int i = k - 1; i >= 0; i--) {
        result[i] = minHeap.poll().getKey();
    }

    return result;
}
```

## 面试题61：和最小的k个数对
### 题目
给你两个递增排序的整数数组，分别从两个数组中各取一个数字u、v形成一个数对(u, v)，请找出和最小的k个数对。例如输入两个数组[1, 5, 13, 21]和[2, 4, 9, 15]，和最小的3个数对为(1, 2)、(1, 4)和(2, 5)。

### 参考代码
#### 解法一
``` java
public List<List<Integer>> kSmallestPairs(int[] nums1,
    int[] nums2, int k) {
    Queue<int[]> maxHeap = new PriorityQueue<>((p1, p2)
        -> p2[0] + p2[1] - p1[0] - p1[1]);
    for (int i = 0; i < Math.min(k, nums1.length); ++i) {
        for (int j = 0; j < Math.min(k, nums2.length); ++j) {
            if (maxHeap.size() >= k) {
                int[] root = maxHeap.peek();
                if (root[0] + root[1] > nums1[i] + nums2[j]) {
                    maxHeap.poll();
                    maxHeap.offer(new int[] {nums1[i], nums2[j]});
                }
            } else {
                maxHeap.offer(new int[] {nums1[i], nums2[j]});
            }
        }
    }

    List<List<Integer>> result = new LinkedList<>();
    while (!maxHeap.isEmpty()) {
        int[] vals = maxHeap.poll();
        result.add(Arrays.asList(vals[0], vals[1]));
    }

    return result;
}
```
 
#### 解法二
``` java
public List<List<Integer>> kSmallestPairs(int[] nums1,
    int[] nums2, int k) {
    Queue<int[]> minHeap = new PriorityQueue<>((p1, p2)
        -> nums1[p1[0]] + nums2[p1[1]] - nums1[p2[0]] - nums2[p2[1]]);
    if (nums2.length > 0) {
        for (int i = 0; i < Math.min(k, nums1.length); ++i) {
            minHeap.offer(new int[] {i, 0});
        }
    }

    List<List<Integer>> result = new ArrayList<>();        
    while (k-- > 0 && !minHeap.isEmpty()) {
        int[] ids = minHeap.poll();
        result.add(Arrays.asList(nums1[ids[0]], nums2[ids[1]]));

        if (ids[1] < nums2.length - 1) {
            minHeap.offer(new int[] {ids[0], ids[1] + 1});
        }
    }

    return result;
}
```
