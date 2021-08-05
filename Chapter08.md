# 第八章：树
## 面试题47：二叉树剪枝
### 题目
一个二叉树的所有结点的值要么是0要么是1，请剪除该二叉树中所有结点的值全都是0 的子树。例如，在剪除图8.2（a）中二叉树中所有结点值都为0的子树之后的结果如图8.2（b）所示。
 
![图8.2](./Figures/0802.png)

图8.2：剪除所有结点值都为0的子树。（a）一个结点值要么是0要么是1的二叉树。（b）剪除所有结点值都为0的子树的结果。

### 参考代码
``` java
public TreeNode pruneTree(TreeNode root) {
    if (root == null) {
        return root;
    }

    root.left = pruneTree(root.left);
    root.right = pruneTree(root.right);
    if (root.left == null && root.right == null && root.val == 0) {
        return null;
    }

    return root;
}
```

## 面试题48：序列化和反序列化二叉树
### 题目
请设计一个算法能将二叉树序列化成一个字符串并能将该字符串反序列化出原来二叉树的算法。

### 参考代码
``` java
public String serialize(TreeNode root) {
    if (root == null) {
        return "#";
    }

    String leftStr = serialize(root.left);
    String rightStr = serialize(root.right);
    return String.valueOf(root.val) + "," + leftStr + "," + rightStr;
}

public TreeNode deserialize(String data) {
    String[] nodeStrs = data.split(",");
    int[] i = {0};
    return dfs(nodeStrs, i);
}

private TreeNode dfs(String[] strs, int[] i) {
    String str = strs[i[0]];
    i[0]++;

    if (str.equals("#")) {
        return null;
    }

    TreeNode node = new TreeNode(Integer.valueOf(str));
    node.left = dfs(strs, i);
    node.right = dfs(strs, i);
    return node;
}
```

## 面试题49：从根结点到叶结点的路径数字之和

### 题目
在一个二叉树里所有结点都在0-9的范围之类，从根结点到叶结点的路径表示一个数字。求二叉树里所有路径表示的数字之和。例如在图8.4中的二叉树有三条从根结点到叶结点的路径，它们分别表示数字395、391和302，这三个数字之和是1088。
 
![图8.4](./Figures/0804.png)

图8.4：一个从根结点到叶结点的路径分别表示数字395、391和302的二叉树。

### 参考代码
``` java
public int sumNumbers(TreeNode root) {
    return dfs(root, 0);      
}

private int dfs(TreeNode root, int path) {
    if (root == null) {
        return 0;
    }

    path = path * 10 + root.val;
    if (root.left == null && root.right == null) {
        return path;
    }

    return dfs(root.left, path) + dfs(root.right, path);
}
```

## 面试题50：向下的路径结点之和
### 题目
给定一个二叉树和一个值sum，求二叉树里结点值之和等于sum的路径的数目。路径的定义为二叉树中沿着指向子结点的指针向下移动所经过的结点，但不一定从根结点开始，也不一定到叶结点结束。例如在图8.5中的二叉树里，有两个路径的结点值之和等于8，其中第一条路径从结点5开始经过结点2到达结点1，第二条路径从结点2开始到结点6。
 
![图8.5](./Figures/0805.png)
 
图8.5：二叉树中有两条路径上的结点值之和等于8，第一条路径从结点5开始经过结点2到达结点1，第二条路径从结点2开始到结点6。

### 参考代码
``` java
public int pathSum(TreeNode root, int sum) {
    Map<Integer, Integer> map = new HashMap<>();
    map.put(0, 1);

    return dfs(root, sum, map, 0);
}

private int dfs(TreeNode root, int sum,
    Map<Integer, Integer> map, int path) {
    if (root == null) {
        return 0;
    }

    path += root.val;
    int count = map.getOrDefault(path - sum, 0);
    map.put(path, map.getOrDefault(path, 0) + 1);

    count += dfs(root.left, sum, map, path);
    count += dfs(root.right, sum, map, path);

    map.put(path, map.get(path) - 1);

    return count;
}
```

## 面试题51：结点之和最大的路径
### 题目
在二叉树中定义路径为从沿着结点间的连接从任意一个结点开始到达任意一个结点所经过的所有结点。路径中至少包含一个结点，不一定经过二叉树的根结点，也不一定经过叶结点。给你非空的一个二叉树，请求出二叉树所有路径上结点值之和的最大值。例如在图8.6中的二叉树中，从结点15开始经过结点20到达结点7的路径是结点值之和为42，是结点值之和最大的路径。
 
![图8.6](./Figures/0806.png)

图8.6：在二叉树中，从结点15开始经过结点20到达结点7的路径是结点值之和为42，是结点值之和最大的路径。

### 参考代码
``` java
public int maxPathSum(TreeNode root) {
    int[] maxSum = {Integer.MIN_VALUE};
    dfs(root, maxSum);
    return maxSum[0];
}

private int dfs(TreeNode root, int[] maxSum) {
    if (root == null) {
        return 0;
    }

    int[] maxSumLeft = {Integer.MIN_VALUE};
    int left = Math.max(0, dfs(root.left, maxSumLeft));

    int[] maxSumRight = {Integer.MIN_VALUE};
    int right = Math.max(0, dfs(root.right, maxSumRight));

    maxSum[0] = Math.max(maxSumLeft[0], maxSumRight[0]);
    maxSum[0] = Math.max(maxSum[0], root.val + left + right);

    return root.val + Math.max(left, right);
}
```

## 面试题52：展平二叉搜索树
### 题目
给你一个二叉搜索树，请调整结点的指针使得每个结点都没有左子结点看起来像一个链表，但新的树仍然是二叉搜索树。例如把图8.8（a）中的二叉搜索树按照这个规则展平之后的结果如图8.8（b）所示。
 
![图8.8](./Figures/0808.png)

图8.8：把二叉搜索树展平成链表。（a）一个有6个结点的二叉树。（b）展平成看起来是链表的二叉搜索树，每个结点都没有左子结点。

### 参考代码
``` java
public TreeNode increasingBST(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode cur = root;
    TreeNode prev = null;
    TreeNode first = null;
    while (cur != null || !stack.isEmpty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }

        cur = stack.pop();
        if (prev != null) {
            prev.right = cur;
        } else {
            first = cur;
        }

        prev = cur;
        cur.left = null;
        cur = cur.right;
    }

    return first;
}
```

## 面试题53：二叉搜索树的下一个结点

### 题目
给你一个二叉搜索时和它的一个结点p，请找出按中序遍历的顺序该结点p的下一个结点。假设二叉搜索树中结点的值都是唯一的。例如在图8.9的二叉搜索树中，结点8的下一个结点是结点9，结点11的下一个结点是null。
 
![图8.9](./Figures/0809.png)

图8.9：在二叉搜索树中，按照中序遍历的顺序结点8的下一个结点是结点9，结点11的下一个结点是null。

### 参考代码
#### 解法一
``` java
public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode cur = root;
    boolean found = false;
    while (cur != null || !stack.isEmpty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }

        cur = stack.pop();
        if (found) {
            break;
        } else if (p == cur) {
            found = true;
        }

        cur = cur.right;
    }

    return cur;
}
```
 
#### 解法二
``` java
public TreeNode inorderSuccessor(TreeNode root, TreeNode p) {
    TreeNode cur = root;
    TreeNode result = null;
    while (cur != null) {
        if (cur.val > p.val) {
            result = cur;
            cur = cur.left;
        } else {
            cur = cur.right;
        }
    }

    return result;
}
```

## 面试题54：所有大于等于结点的值之和
### 题目
给你一个二叉搜索树，请将它的每个结点的值替换成树中大于或者等于该结点值的所有结点值之和。假设二叉搜索树中结点的值唯一。例如，输入图8.10（a）中的二叉搜索树，由于有两个结点的值大于或者等于6（即结点6和结点7），因此值为6结点的值替换成13，其他结点的值的替换过程类似，所有结点的值替换之后的结果如图8.10（b）所示。
 
![图8.10](./Figures/0810.png)

图8.10：把二叉搜索树中每个结点的值替换成树中大于或者等于该结点值的所有结点值之和。（a）一个二叉搜索树。（b）替换之后的二叉树。

### 参考代码
``` java
public TreeNode convertBST(TreeNode root) {
    Stack<TreeNode> stack = new Stack<>();
    TreeNode cur = root;
    int sum = 0;
    while (cur != null || !stack.isEmpty()) {
        while (cur != null) {
            stack.push(cur);
            cur = cur.right;
        }

        cur = stack.pop();
        sum += cur.val;
        cur.val = sum;
        cur = cur.left;
    }

    return root;
}
```

## 面试题55：二叉搜索树迭代器
### 题目
请实现二叉搜索树的迭代器BSTIterator，它主要有如下三个函数：
+ 构造函数输入一个二叉搜索树的根结点初始化该迭代器；
+ 函数next返回二叉搜索树中下一个最小的结点的值；
+ 函数hasNext返回二叉搜索树是否还有下一个结点。

例如输入图8.11中的二叉树搜索树初始化BSTIterator，第一次调用函数next将返回最小的结点值1，此时调用函数hasNext返回true。再次调用函数next将返回下一个最小的结点的值2，此时再调用函数hasNext将返回true。第三次调用函数next将返回下一个最小的结点的值3，如果此时再调用函数hasNext将返回false。
 
![图8.11](./Figures/0811.png)
 
图8.11：一个有3个结点的二叉搜索树。

### 参考代码
``` java
public class BSTIterator {
    TreeNode cur;
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        cur = root;
        stack = new Stack<>();
    }

    public boolean hasNext() {
        return cur != null || !stack.isEmpty();
    }

    public int next() {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        
        cur = stack.pop();
        int val = cur.val;
        cur = cur.right;
        
        return val;
    }
}
```

## 面试题56：二叉搜索树中两个结点之和
### 题目
给你一个二叉搜索树和一个值k，请判断该二叉搜索树中是否存在两个结点它们的值之和等于k。假设二叉搜索树中结点的值均唯一。例如在图8.12中的二叉搜索树里，存在两个两个结点它们的和等于12（结点5和结点7），但不存在两个结点值之和为22的结点。 
 
![图8.12](./Figures/0812.png)
 
图8.12：在二叉搜索树中，存在两个结点它们的和等于12（结点5和结点7），但不存在两个结点值之和为22的结点。

### 参考代码
``` java
public class BSTIterator {
    TreeNode cur;
    Stack<TreeNode> stack;

    public BSTIterator(TreeNode root) {
        cur = root;
        stack = new Stack<>();
    }

    public boolean hasNext() {
        return cur != null || !stack.isEmpty();
    }

    public int next() {
        while (cur != null) {
            stack.push(cur);
            cur = cur.left;
        }
        
        cur = stack.pop();
        int val = cur.val;
        cur = cur.right;
        
        return val;
    }
}

public class BSTIteratorReversed {
    TreeNode cur;
    Stack<TreeNode> stack;

    public BSTIteratorReversed(TreeNode root) {
        cur = root;
        stack = new Stack<>();
    }

    public boolean hasPrev() {
        return cur != null || !stack.isEmpty();
    }

    public int prev() {
        while (cur != null) {
            stack.push(cur);
            cur = cur.right;
        }
        
        cur = stack.pop();
        int val = cur.val;
        cur = cur.left;
        
        return val;
    }
}

public boolean findTarget(TreeNode root, int k) {
    if (root == null) {
        return false;
    }

    BSTIterator iterNext = new BSTIterator(root);
    BSTIteratorReversed iterPrev = new BSTIteratorReversed(root);
    int next = iterNext.next();
    int prev = iterPrev.prev();
    while (next != prev) {
        if (next + prev == k) {
            return true;
        }

        if (next + prev < k) {
            next = iterNext.next();
        } else {
            prev = iterPrev.prev();
        }
    }

    return false;
}
```

## 面试题57：值和下标之差都在给定的范围内
### 题目
给你一个整数数组nums，请判断是否存在两个不同的下标i和j（i和j之差的绝对值不大于给定的k）使得两个数值nums[i]和nums[j]的差的绝对值不大于给定的t。例如，如果输入数组{1, 2, 3, 1}，k为3，t为0，由于下标0和下标3，它们对应的数字之差的绝对值为0，因此返回true。如果输入数组{1, 5, 9, 1, 5, 9}，k为2，t为3，由于不存在两个下标之差小于等于2的数字它们差的绝对值小于等于3，此时应该返回false。

### 参考代码
#### 解法一
``` java
public boolean containsNearbyAlmostDuplicate(int[] nums,
    int k, int t) {
    TreeSet<Long> set = new TreeSet<>();
    for (int i = 0; i < nums.length; ++i) {
        Long lower = set.floor((long)nums[i]);
        if (lower != null && lower >= (long)nums[i] - t) {
            return true;
        }

        Long upper = set.ceiling((long)nums[i]);
        if (upper != null && upper <= (long)nums[i] + t) {
            return true;
        }

        set.add((long)nums[i]);
        if (i >= k) {
            set.remove((long)nums[i - k]);
        }
    }

    return false;
}
```

#### 解法二
``` java
public boolean containsNearbyAlmostDuplicate(int[] nums,
    int k, int t) {
    Map<Integer, Integer> buckets = new HashMap<>();
    int bucketSize = t + 1;
    for (int i = 0; i < nums.length; i++) {
        int num = nums[i];
        int id = getBucketID(num, bucketSize);

        if (buckets.containsKey(id)
            || (buckets.containsKey(id-1) && buckets.get(id-1) + t >= num)
            || (buckets.containsKey(id+1) && buckets.get(id+1) - t <= num)) {
            return true;
        }

        buckets.put(id, num);
        if (i >= k) {
            buckets.remove(getBucketID(nums[i - k], bucketSize));
        }
    }

    return false;
}

private int getBucketID(int num, int bucketSize) {
    return num >= 0
        ? num / bucketSize
        : (num + 1) / bucketSize - 1; 
}
```

## 面试题58：日程表
### 题目
请实现一个类型MyCalendar用来记录你的日程安排，该类型有一个方法book(int start, int end)往日程安排表里添加一个时间区域为[start, end)的事项（这是个半开半闭区间，即start<=x<end）。如果[start, end)内没有事先安排其他事项，则成功添加该事项并返回true。否则不能添加该事项，并返回false。

例如，在下面的三次调用book方法中，第二次调用返回false，这是因为时间[15, 20)已经被第一次调用预留了。由于第一次占用的时间是个半开半闭区间，并没有真正占用时间20，因此不影响第三次调用预留时间区间[20, 30)。 
``` java
MyCalendar cal = new MyCalendar()；
cal.book(10, 20); // returns true
cal.book(15, 25); // returns false
cal.book(20, 30); // returns true
```

### 参考代码
``` java
class MyCalendar {
    private TreeMap<Integer, Integer> events;
    
    public MyCalendar() {
        events = new TreeMap<>();
    }
    
    public boolean book(int start, int end) {
        Map.Entry<Integer, Integer> event = events.floorEntry(start);
        if (event != null && event.getValue() > start) {
            return false;
        }
        
        event = events.ceilingEntry(start);
        if (event != null && event.getKey() < end) {
            return false;
        }
        
        events.put(start, end);
        return true;
    }
}
```
