# 第三章：字符串
## 面试题14：字符串中的变位词
### 题目
输入两个字符串s1和s2，如何判断s2中是否包含s1的某个变位词？如果s2中包含s1的某个变位词，则s1至少有一个变位词是s2的子字符串。假设两个输入字符串中只包含英语小写字母。例如输入字符串s1为"ab"，s2为"dgcaf"，由于s2中包含s1的变位词"ba"，因此输出是true。如果输入字符串s1为"ac"，s2为"dcgaf"，输出为false。

### 参考代码
``` java
public boolean checkInclusion(String s1, String s2) {
    if (s2.length() >= s1.length()) {
        int[] counts = new int[26];
        for (int i = 0; i < s1.length(); ++i) {
            counts[s1.charAt(i) - 'a']++;
            counts[s2.charAt(i) - 'a']--;
        }

        if (areAllZero(counts)) {
            return true;
        }

        for (int i = s1.length(); i < s2.length(); ++i) {
            counts[s2.charAt(i) - 'a']--;
            counts[s2.charAt(i - s1.length()) - 'a']++;
            if (areAllZero(counts)) {
                return true;
            }
        }
    }

    return false;
}

private boolean areAllZero(int[] counts) {
    for (int count : counts) {
        if (count != 0) {
            return false;
        }
    }

    return true;
}
```

## 面试题15：字符串中的所有变位词
### 题目
输入两个字符串s1和s2，如何找出s2的所有变位词在s1中的起始下标？假设两个输入字符串中只包含英语小写字母。例如输入字符串s1为"cbadabacg"，s2为"abc"，s2有两个变位词"cba"和"bac"是s1中的字符串，输出它们在s1中的起始下标0和5。

### 参考代码
``` java
    public List<Integer> findAnagrams(String s1, String s2) {
        List<Integer> indices = new LinkedList<>();
        if (s1.length() >= s2.length()) {
            int[] counts = new int[26];
            for (int i = 0; i < s2.length(); ++i) {
                counts[s2.charAt(i) - 'a']++;
                counts[s1.charAt(i) - 'a']--;
            }

            if (areAllZero(counts)) {
                indices.add(0);
            }

            for (int i = s2.length(); i < s1.length(); ++i) {
                counts[s1.charAt(i) - 'a']--;
                counts[s1.charAt(i - s2.length()) - 'a']++;
                if (areAllZero(counts)) {
                    indices.add(i - s2.length() + 1);
                }
            }
        }
        
        return indices;
    }
    
    private boolean areAllZero(int[] counts) {
        for (int count : counts) {
            if (count != 0) {
                return false;
            }
        }
        
        return true;
    }
```

## 面试题16：不含重复字符的最长子字符串
### 题目
输入一个字符串，求该字符串中不含重复字符的最长连续子字符串的长度。例如，输入字符串"babcca"，它最长的不含重复字符串的子字符串是"abc"，长度为3。

### 参考代码
#### 解法一
``` java
public int lengthOfLongestSubstring(String s) {
    int[] counts = new int[256];
    int longest = s.length() > 0 ? 1 : 0;
    for (int i = 0, j = -1; i < s.length(); ++i) {
        counts[s.charAt(i)]++;
        while (hasGreaterThan1(counts)) {
            ++j;
            counts[s.charAt(j)]--;
        }

        longest = Math.max(i - j, longest);
    }

    return longest;
}

private boolean hasGreaterThan1(int[] counts) {
    for (int count : counts) {
        if (count > 1) {
            return true;
        }
    }

    return false;
}    
```
#### 解法二
``` java
public int lengthOfLongestSubstring(String s) {
    int[] counts = new int[256];
    int longest = s.length() > 0 ? 1 : 0;
    int countDup = 0;
    for (int i = 0, j = -1; i < s.length(); ++i) {
        counts[s.charAt(i)]++;
        if (counts[s.charAt(i)] == 2) {
            countDup++;
        }

        while (countDup > 0) {
            ++j;
            counts[s.charAt(j)]--;
            if (counts[s.charAt(j)] == 1) {
                countDup--;
            }
        }

        longest = Math.max(i - j, longest);
    }

    return longest;
}
```

## 面试题17：含有所有字符的最短字符串
### 题目
输入两个字符串s和t，请找出s中包含t的所有字符的最短子字符串。例如输入s为字符串"ADDBANCAD"，t为字符串"ABC"，则s中包含字符'A'、'B'、'C'的最短子字符串是"BANC"。如果不存在符合条件的子字符串，返回空字符串""。如果存在多个符合条件的子字符串，返回任意一个。

### 参考代码
``` java
public String minWindow(String s, String t) {
    HashMap<Character, Integer> charToCount = new HashMap<>();
    for (char ch : t.toCharArray()) {
        charToCount.put(ch, charToCount.getOrDefault(ch, 0) + 1);
    }

    int count = charToCount.size();
    int start = 0, end = 0, minStart = 0, minEnd = 0;
    int minLength = Integer.MAX_VALUE;
    while (end < s.length() || (count == 0 && end == s.length())) {
        if (count > 0) {
            char endCh = s.charAt(end);
            if (charToCount.containsKey(endCh)) {
                charToCount.put(endCh, charToCount.get(endCh) - 1);
                if (charToCount.get(endCh) == 0) {
                    count--;
                }
            }

            end++;
        } else {
            if (end - start < minLength) {
                minLength = end - start;
                minStart = start;
                minEnd = end;
            }

            char startCh = s.charAt(start);
            if (charToCount.containsKey(startCh)) {
                charToCount.put(startCh, charToCount.get(startCh) + 1);
                if (charToCount.get(startCh) == 1) {
                    count++;
                }
            }

            start++;
        }
    }

    return minLength < Integer.MAX_VALUE
        ? s.substring(minStart, minEnd)
        : "";
}
```

## 面试题18：有效的回文
### 题目
给定一个字符串，请判断它是不是一个回文字符串。我们只需要考虑字母或者数字字符，并忽略大小写。例如，"A man, a plan, a canal: Panama"是一个回文字符串，而"race a car"不是。

### 参考代码
``` java
public boolean isPalindrome(String s) {
    int i = 0;
    int j = s.length() - 1;
    while (i < j) {
        char ch1 = s.charAt(i);
        char ch2 = s.charAt(j);
        if (!Character.isLetterOrDigit(ch1)) {
            i++;
        } else if (!Character.isLetterOrDigit(ch2)) {
            j--;
        } else {
            ch1 = Character.toLowerCase(ch1);
            ch2 = Character.toLowerCase(ch2);
            if (ch1 != ch2) {
                return false;
            }

            i++;
            j--;
        }       
    }

    return true;
}
```

## 面试题19：最多删除一个字符得到回文
### 题目
给定一个字符串，请判断如果最多从字符串中删除一个字符能不能得到一个回文字符串。例如，如果输入字符串"abca"，由于删除字符'b'或者'c'就能得到一个回文字符串，因此输出为true。

### 参考代码
``` java
public boolean validPalindrome(String s) {
    int start = 0;
    int end = s.length() - 1;
    for (; start < s.length() / 2; ++start, --end) {
        if (s.charAt(start) != s.charAt(end)) {
            break;
        }
    }

    return start == s.length() / 2
        || isPalindrome(s, start, end - 1)
        || isPalindrome(s, start + 1, end);
}

private boolean isPalindrome(String s, int start, int end) {
    while (start < end) {
        if (s.charAt(start) != s.charAt(end)) {
            break;
        }

        start++;
        end--;
    }

    return start >= end;
}
```

## 面试题20：回文子字符串的个数
### 题目
给定一个字符串，请问字符串里有多少回文连续子字符串？例如，字符串里"abc"有3个回文字符串，分别为"a"、"b"、"c"；而字符串"aaa"里有6个回文子字符串，分别为"a"、"a"、"a"、"aa"、"aa"和"aaa"。

### 参考代码
``` java
    public int countSubstrings(String s) {
        if (s == null || s.length() == 0) {
            return 0;
        }
        
        int count = 0;
        for (int i = 0; i < s.length(); ++i) {
            count += countPalindrome(s, i, i);
            count += countPalindrome(s, i, i + 1);
        }
        
        return count;
    }
    
    private int countPalindrome(String s, int start, int end) {
        int count = 0;
        while (start >= 0 && end < s.length()
               && s.charAt(start) == s.charAt(end)) {
            count++;
            start--;
            end++;
        }
        
        return count;
    }
```
