# 第五章：哈希表
## 面试题30：插入、删除和随机访问都是O(1)的容器
### 题目
设计一个数据结构，使得如下三个操作的时间复杂度都是O(1)：
+ insert(value)：如果数据集不包含一个数值，则把它添加到数据集；
+ remove(value)：如果数据集包含一个数值，则把它删除；
+ getRandom()：随机返回数据集中的一个数值，要求数据集里每个数字被返回的概率都相同。

### 参考代码
``` java
class RandomizedSet {
    public RandomizedSet() {
        numToLocation = new HashMap<>();
        nums = new ArrayList<>();
    }
    
    public boolean insert(int val) {
        if (numToLocation.containsKey(val)) {
            return false;
        }

        numToLocation.put(val, nums.size());
        nums.add(val);
        return true;
    }
    
    public boolean remove(int val) {
        if (!numToLocation.containsKey(val)) {
            return false;
        }
        
        int location = numToLocation.get(val);
        numToLocation.put(nums.get(nums.size() - 1), location);
        numToLocation.remove(val);
        nums.set(location, nums.get(nums.size() - 1));
        nums.remove(nums.size() - 1);
        return true;
    }
    
    public int getRandom() {
        Random random = new Random();
        int r = random.nextInt(nums.size());
        return nums.get(r);
    }
}
```

## 面试题31：最近最少使用缓存
### 题目
请设计实现一个最近最少使用（Least Recently Used，LRU）缓存，要求如下两个操作的时间复杂度都是O(1)：
+ get(key)：如果缓存中存在键值key，则返回它对应的值；否则返回-1。
+ put(key, value)：如果缓存中之前包含键值key，将它的值设为value；否则添加键值key及对应的值value。在添加一个键值时如果缓存容量已经满了，则在添加新键值之前删除最近最少使用的键值（缓存里最长时间没有被使用过的元素）。

### 参考代码
``` java
class ListNode{
    public int key;
    public int value;
    public ListNode next;
    public ListNode prev;

    public ListNode(int k, int v) {
        key = k;
        value = v;
    }
}
    
class LRUCache {
    private ListNode head;
    private ListNode tail;
    private Map<Integer, ListNode> map;
    int capacity;
    
    public LRUCache(int cap) {
        map = new HashMap<>();
        
        head = new ListNode(-1, -1);
        tail = new ListNode(-1, -1);
        head.next = tail;
        tail.prev = head;
        
        capacity = cap;
    }
    
    public int get(int key) {
        ListNode node = map.get(key);
        if (node == null) {
            return -1;
        }
        
        moveToTail(node, node.value);
        
        return node.value;
    }
    
    public void put(int key, int value) {
        if (map.containsKey(key)) {
            moveToTail(map.get(key), value);
        } else {
            if (map.size() == capacity) {
                ListNode toBeDeleted = head.next;
                deleteNode(toBeDeleted);
                
                map.remove(toBeDeleted.key);
            }
            
            ListNode node = new ListNode(key, value);
            insertToTail(node);
            
            map.put(key, node);
        }
    }
    
    private void moveToTail(ListNode node, int newValue) {
        deleteNode(node);

        node.value = newValue;
        insertToTail(node);
    }
    
    private void deleteNode(ListNode node) {
        node.prev.next = node.next;
        node.next.prev = node.prev;
    }
    
    private void insertToTail(ListNode node) {
        tail.prev.next = node;
        node.prev = tail.prev;
        node.next = tail;
        tail.prev = node;
    }
}
```

## 面试题32：有效的变位词
### 题目
给定两个字符串s和t，请判断它们是不是一组变位词。在一组变位词中，如果它们中的字符以及每个字符出现的次数都相同，但字符的顺序不能。例如"anagram"和"nagaram"就是一组变位词。

### 参考代码
#### 解法一
``` java
public boolean isAnagram(String str1, String str2) {
    if (str1.length() != str2.length())
        return false;

    int[] counts = new int[26];
    for (char ch : str1.toCharArray()) {
        counts[ch - 'a']++;
    }

    for (char ch : str2.toCharArray()) {
        if (counts[ch - 'a'] == 0) {
            return false;
        }

        counts[ch - 'a']--;
    }

    return true;
}
```

#### 解法二
``` java
public boolean isAnagram(String str1, String str2) {
    if (str1.length() != str2.length()) {
        return false;
    }

    Map<Character, Integer> counts = new HashMap<>();
    for (char ch : str1.toCharArray()) {
        counts.put(ch, counts.getOrDefault(ch, 0) + 1);
    }

    for (char ch : str2.toCharArray()) {
        if (counts.getOrDefault(ch, 0) == 0)
            return false;

        counts.put(ch, counts.get(ch) - 1);
    }

    return true;
}
```

## 面试题33：变位词组
### 题目
给定一组单词，请将它们按照变位词分组。例如输入一组单词["eat", "tea", "tan", "ate", "nat", "bat"]，则可以分成三组，分别是["eat", "tea", "ate"]、["tan", "nat"]和["bat"]。假设单词中只包含小写的英文字母。

### 参考代码
#### 解法一
``` java
public List<List<String>> groupAnagrams(String[] strs) {
    int hash[] = {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 
        43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97, 101};

    Map<Long, List<String>> groups = new HashMap<>();
    for (String str : strs) {
        long wordHash = 1;
        for(int i = 0; i < str.length(); ++i) {
            wordHash *= hash[str.charAt(i) - 'a'];
        }

        groups.putIfAbsent(wordHash, new LinkedList<String>());
        groups.get(wordHash).add(str);
    }

    return new LinkedList<>(groups.values());
}
```

#### 解法二
``` java
public List<List<String>> groupAnagrams(String[] strs) {
    Map<String, List<String>> groups = new HashMap<>();
    for (String str : strs) {
        char[] charArray = str.toCharArray();
        Arrays.sort(charArray);
        String sorted = new String(charArray);
        groups.putIfAbsent(sorted, new LinkedList<String>());
        groups.get(sorted).add(str);
    }

    return new LinkedList<>(groups.values());
}
```

## 面试题34：外星语言是否排序
### 题目
有一门外星语言，它的字母表刚好包含所有的英文小写字母，只是字母表的顺序不同。给定一组单词和字母表顺序，请判断这些单词是否按照字母表的顺序排序。例如，输入一组单词["offer", "is", "coming"]，以及字母表顺序"zyxwvutsrqponmlkjihgfedcba"，由于字母'o'在字母表中位于'i'的前面，所以单词"offer"排在"is"的前面；同样由于字母'i'在字母表中位于'c'的前面，所以单词"is"排在"coming"的前面。因此这一组单词是按照字母表顺序排序的，应该输出true。

### 参考代码
``` java
public boolean isAlienSorted(String[] words, String order) {
    int[] orderArray = new int[order.length()];
    for (int i = 0; i < order.length(); ++i) {
        orderArray[order.charAt(i) - 'a'] = i;
    }

    for (int i = 0; i < words.length - 1; ++i) {
        if (!isSorted(words[i], words[i + 1], orderArray)) {
            return false;
        }
    }

    return true;
}

private boolean isSorted(String word1, String word2, int[] orderArray) {
    int i = 0;
    for (; i < word1.length() && i < word2.length(); ++i) {
        char ch1 = word1.charAt(i);
        char ch2 = word2.charAt(i);
        if (orderArray[ch1 - 'a'] < orderArray[ch2 - 'a']) {
            return true;
        }

        if (orderArray[ch1 - 'a'] > orderArray[ch2 - 'a']) {
            return false;
        }
    }

    return i == word1.length();
}
```

## 面试题35：最小时间差
### 题目
给你一组范围在00:00至23:59的时间，求它们任意两个时间之间的最小时间差。例如，输入时间数组["23:50", "23:59", "00:00"]，"23:59"和"00:00"之间只有1分钟间隔，是最小的时间差。

### 参考代码
``` java
public int findMinDifference(List<String> timePoints) {
    if (timePoints.size() > 1440) {
        return 0;
    }

    boolean minuteFlags[] = new boolean[1440];
    for (String time : timePoints) {
        String t[] = time.split(":");
        int minute = Integer.parseInt(t[0]) * 60 + Integer.parseInt(t[1]);
        if (minuteFlags[minute]) {
            return 0;
        }

        minuteFlags[minute] = true;
    }

    return findMinDifference(minuteFlags);
}

private int findMinDifference(boolean minuteFlags[]) {
    int minDiff = minuteFlags.length - 1;
    int prev = -1;
    int first = minuteFlags.length - 1;
    int last = -1;
    for (int i = 0; i < minuteFlags.length; ++i) {
        if (minuteFlags[i]) {
            if (prev >= 0) {
                minDiff = Math.min(i - prev, minDiff);
            }

            prev = i;
            first = Math.min(i, first);
            last = Math.max(i, last);
        }
    }

    minDiff = Math.min(first + minuteFlags.length - last, minDiff);
    return minDiff;
}
```
