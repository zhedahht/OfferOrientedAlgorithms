# 第一章：整数

## 面试题1：整数除法
### 题目
输入两个int型整数，求它们除法的商，要求不得使用乘号'*'、除号'/'以及求余符号'%'。当发生溢出时返回最大的整数值。假设除数不为0。例如，输入15和2，输出15/2的结果，即7。

### 参考代码
``` java
public int divide(int dividend, int divisor) {
    if (dividend == 0x80000000 && divisor == -1){
        return Integer.MAX_VALUE;
    }

    int negative = 2;
    if (dividend > 0) {
        negative--;
        dividend = -dividend;
    }

    if (divisor > 0) {
        negative--;
        divisor = -divisor;
    }

    int result = divideCore(dividend, divisor);
    return negative == 1 ? -result : result;
}

private int divideCore(int dividend, int divisor) {
    int result = 0;
    while (dividend <= divisor) {
        int value = divisor;
        int quotient = 1;
        while (value >= 0xc0000000 && dividend <= value + value) {
            quotient += quotient;
            value += value;
        }

        result += quotient;
        dividend -= value;
    }

    return result;
}
```

## 面试题2：二进制加法
### 题目
输入两个表示二进制的字符串，请计算它们的和，并以二进制字符串的形式输出。例如输入的二进制字符串分别是"11"和"10"，则输出"101"。

### 参考代码
``` java
public String addBinary(String a, String b) {
    StringBuilder result = new StringBuilder();
    int i = a.length() - 1;
    int j = b.length() - 1;
    int carry = 0;
    while (i >= 0 || j >= 0) {
        int digitA = i >= 0 ? a.charAt(i--) - '0' : 0;
        int digitB = j >= 0 ? b.charAt(j--) - '0' : 0;
        int sum = digitA + digitB + carry;
        carry = sum >= 2 ? 1 : 0;
        sum = sum >= 2 ? sum - 2 : sum;
        result.append(sum);
    }

    if (carry == 1) {
        result.append(1);
    }

    return result.reverse().toString();
}
```

## 面试题3：前n个数字二进制中1的个数
### 题目
输入一个非负数n，请计算0到n之间每个数字的二进制表示中1的个数，并输出一个数组。例如，输入n为4，由于0、1、2、3、4的二进制表示的1的个数分别为0、1、1、2、1，因此输出数组[0, 1, 1, 2, 1]。

### 参考代码
#### 解法一
``` java
public int[] countBits(int num) {
    int[] result = new int[num + 1];
    for (int i = 0; i <= num; ++i) {
        int count = 0;
        int j = i;
        while (j != 0) {
            result[i]++;
            j = j & (j - 1);
        }
    }

    return result;
}
```
#### 解法二
``` java
public int[] countBits(int num) {
    int[] result = new int[num + 1];
    for (int i = 1; i <= num; ++i) {
        result[i] = result[i & (i - 1)] + 1;
    }

    return result;
}
```

#### 解法三
``` java
public int[] countBits(int num) {
    int[] result = new int[num + 1];
    for (int i = 1; i <= num; ++i) {
        result[i] = result[i >> 1] + (i & 1);
    }

    return result;
}
```

## 面试题4：只出现一次的数字
### 题目
输入一个整数数组，数组中除一个数字只出现一次之外其他数字都出现三次。请找出那个唯一只出现一次的数字。例如，如果输入的数组为[0, 1, 0, 1, 0, 1, 100]，则只出现一次的数字时100。

### 参考代码
``` java
public int singleNumber(int[] nums) {
    int[] bitSums = new int[32];
    for (int num : nums) {
        for (int i = 0; i < 32; i++) {
            bitSums[i] += (num >> (31 - i)) & 1;
        }
    }

    int result = 0;
    for (int i = 0; i < 32; i++) {
        result = (result << 1) + bitSums[i] % 3;
    }

    return result;
}
```

## 面试题5：单词长度的最大乘积
### 题目
输入一个字符串数组words，请计算当两个字符串words[i]和words[j]不包含相同字符时它们长度的乘积的最大值。如果没有不包含相同字符的一对字符串，那么返回0。假设字符串中只包含英语的小写字母。例如，输入的字符串数组words为["abcw", "foo", "bar", "fxyz","abcdef"]，数组中的字符串"bar"与"foo"没有相同的字符，它们长度的乘积为9。"abcw"与" fxyz "也没有相同的字符，它们长度的乘积是16，这是不含相同字符的一对字符串的长度乘积的最大值。

### 参考代码
``` java
public int maxProduct(String[] words) {
    boolean[][] flags = new boolean[words.length][26];
    for (int i = 0; i < words.length; i++) {
        for(char c: words[i].toCharArray()) {
            flags[i][c - 'a'] = true;
        }
    }

    int result = 0;
    for (int i = 0; i < words.length; i++) {
        for (int j = i + 1; j < words.length; j++) {
            int k = 0;
            for (; k < 26; k++) {
                if (flags[i][k] && flags[j][k]) {
                    break;
                }
            }

            if (k == 26) {
                int prod = words[i].length() * words[j].length();
                result = Math.max(result, prod);
            }
        }
    }

    return result;
}
```
