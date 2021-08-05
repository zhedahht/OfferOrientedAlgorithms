# 第十章：前缀树
## 面试题62：实现前缀树
### 题目
请设计实现一个前缀树Trie，它有如下操作：
+ 函数insert，往前缀树里添加一个字符串。
+ 函数search，查找字符串。如果前缀树里包含该字符串，返回true；否则返回false。
+ 函数startWith，查找字符串前缀。如果前缀树里包含以该前缀开头的字符串，返回true；否则返回false。

例如，调用函数insert往前缀树里添加单词"goodbye"之后，输入"good"调用函数search返回false，但输入"good"调用函数startWide返回true。再次调用函数insert添加单词"good"之后，此时再输入"good"调用函数search则返回true。

### 参考代码
``` java
class Trie {
    private TrieNode root;

    static class TrieNode {
        TrieNode children[];
        boolean isWord;
        
        public TrieNode() {
            children = new TrieNode[26];
        }
    }
    
    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            if (node.children[ch - 'a'] == null) {
                node.children[ch - 'a'] = new TrieNode();
            }
            
            node = node.children[ch - 'a'];
        }
        
        node.isWord = true;
    }
    
    public boolean search(String word) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            if (node.children[ch - 'a'] == null) {
                return false;
            }
            
            node = node.children[ch - 'a'];
        }
        
        return node.isWord;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode node = root;
        for (char ch : prefix.toCharArray()) {
            if (node.children[ch - 'a'] == null) {
                return false;
            }
            
            node = node.children[ch - 'a'];
        }
        
        return true;
    }
}
```

## 面试题63：替换单词
### 题目
英语里有一个概念叫词根。我们在词根后面加上若干字符就能拼出更长的单词。例如"an"是一个词根，在它后面加上"other"就能得到另一个单词"another"。现在给你一个由词根组成的字典和一个英语句子，如果句子中的单词在字典里有它的词根，则用它的词根替换该单词；如果单词没有词根，则保留该单词。请输出替换后的句子。例如，如果词根字典包含字符串["cat", "bat", "rat"]，英语句子为"the cattle was rattled by the battery"，则替换之后的句子是"the cat was rat by the bat"。

### 参考代码
``` java
static class TrieNode {
    public TrieNode[] children;
    public boolean isWord;

    public TrieNode() {
        children = new TrieNode[26];
    }
}

public String replaceWords(List<String> dict, String sentence) {
    TrieNode root = buildTrie(dict);            
    StringBuilder builder = new StringBuilder();

    String[] words = sentence.split(" ");
    for (int i = 0; i < words.length; i++) {
        String prefix = findPrefix(root, words[i]);
        if (!prefix.isEmpty()) {
            words[i] = prefix;
        }   
    }

    return String.join(" ", words);
}

private TrieNode buildTrie(List<String> dict) {
    TrieNode root = new TrieNode();
    for (String word : dict) {
        TrieNode node = root;
        for (char ch : word.toCharArray()) {
            if (node.children[ch - 'a'] == null) {
                node.children[ch - 'a'] = new TrieNode();
            }

            node = node.children[ch - 'a'];
        }

        node.isWord = true;
    }

    return root;
}

private String findPrefix(TrieNode root, String word) {
    TrieNode node = root;
    StringBuilder builder = new StringBuilder();
    for (char ch : word.toCharArray()) {
        if (node.isWord || node.children[ch - 'a'] == null) {
            break;
        }

        builder.append(ch);
        node = node.children[ch - 'a'];
    }

    return node.isWord ? builder.toString() : "";
}
```

## 面试题64：神奇的字典
### 题目
请实现有如下两个操作的神奇字典：
+ 函数buildDict，输入单词数组用来创建一个字典。
+ 函数search，输入一个单词，判断能否修改该单词中的一个字符使得修改之后的单词是字典中的一个单词。

例如输入["happy", "new", "year"]创建一个神奇字典。如果输入单词"now"进行search操作，由于将其中的'o'修改成'e'就得到字典中的"new"，因此返回true。如果输入单词"new"，将其中任意字符修改成另一不同的字符都不能得到字典里的单词，因此返回false。

### 参考代码
``` java
class MagicDictionary {
    static class TrieNode {
        public TrieNode[] children;
        public boolean isWord;
        
        public TrieNode() {
            children = new TrieNode[26];
        }
    }
    
    TrieNode root;

    public MagicDictionary() {
        root = new TrieNode();
    }
    
    public void buildDict(String[] dict) {
        for (String word : dict) {
            TrieNode node = root;
            for (char ch : word.toCharArray()) {
                if (node.children[ch - 'a'] == null) {
                    node.children[ch - 'a'] = new TrieNode();
                }
                
                node = node.children[ch - 'a'];
            }
            
            node.isWord = true;
        }
    }
    
    public boolean search(String word) {
        return dfs(root, word, 0, 0);
    }

    private boolean dfs(TrieNode root, String word, int i, int edit) {
        if (root == null) {
            return false;
        }
        
        if (root.isWord && i == word.length() && edit == 1) {
            return true;
        }

        if (i < word.length() && edit <= 1) {
            boolean found = false;
            for (int j = 0; j < 26 && !found; j++) {
                int next = j == word.charAt(i) - 'a' ? edit : edit + 1;
                found = dfs(root.children[j], word, i + 1, next);
            }

            return found;
        }

        return false;
    }
}
```

## 面试题65：最短的单词编码
### 题目
给定一个含有n个单词的数组，我们可以把它们编码成一个字符串和n个下标。假如给定单词数组["time", "me", "bell"]，我们可以把它们编码成一个字符串"time#bell#"，然后这些单词就可以通过下标[0, 2, 5]得到。对于每一个下标，我们都可以从编码之后得到的字符串中相应的位置开始扫描直到遇到"#"字符前所经过的子字符串为单词数组中的一个单词。例如从"time#bell#"下标2的位置开始扫描直到遇到"#"前经过子字符串"me"是给定单词数组的第二个单词。

给我们一个单词数组，请问按照上述规则把这些单词编码之后得到的最短字符串的长度是多少？如果输入是字符串数组["time", "me", "bell"]，编码之后最短的字符串是"time#bell#"，长度是10。

### 参考代码
``` java
static class TrieNode {
    public TrieNode[] children;
    public TrieNode() {
        children = new TrieNode[26];
    }
}

public int minimumLengthEncoding(String[] words) {
    TrieNode root = buildTrie(words);

    int total[] = {0};
    dfs(root, 1, total);
    return total[0];
}

private TrieNode buildTrie(String[] words) {
    TrieNode root = new TrieNode();
    for (String word : words) {
        TrieNode node = root;
        for (int i = word.length() - 1; i >= 0; i--) {
            char ch = word.charAt(i);
            if (node.children[ch - 'a'] == null) {
                node.children[ch - 'a'] = new TrieNode();
            }

            node = node.children[ch - 'a'];
        }
    }

    return root;        
}

private void dfs(TrieNode root, int length, int[] total) {
    boolean isLeaf = true;
    for (TrieNode child : root.children) {
        if (child != null) {
            isLeaf = false;
            dfs(child, length + 1, total);
        }
    }

    if (isLeaf) {
        total[0] += length;
    }
}
```

## 面试题66：单词之和
### 题目
请设计实现一个类型MapSum，它有如下两个操作：
+ 函数insert，输入一个字符串和一个整数，往数据集合中添加一个字符串以及它对应的值。如果数据集合中已经包含该字符串，则将该字符串对应的值替换成新值。
+ 函数sum，输入一个字符串，返回数据集合里所有以该字符串为前缀的字符串对应值之和。

例如，第一次调用函数insert添加字符串"happy"和它的值3，此时如果输入"hap"调用sum则返回3。第二次再用函数insert添加字符串"happen"和它的值2，此时如果输入"hap"调用sum则返回5。

### 参考代码
``` java
class MapSum {
    
    static class TreeNode {
        public TreeNode[] children;
        public int value;
        
        public TreeNode() {
            children = new TreeNode[26];
        }
    }
    
    private TreeNode root;

    public MapSum() {
        root = new TreeNode();
    }
    
    public void insert(String key, int val) {
        TreeNode node = root;
        for (int i = 0; i < key.length(); ++i) {
            char ch = key.charAt(i);
            if (node.children[ch - 'a'] == null) {
                node.children[ch - 'a'] = new TreeNode();
            }
            
            node = node.children[ch - 'a'];
        }
        
        node.value = val;
    }
    
    public int sum(String prefix) {
        TreeNode node = root;
        for (int i = 0; i < prefix.length(); ++i) {
            char ch = prefix.charAt(i);
            if (node.children[ch - 'a'] == null) {
                return 0;
            }
            
            node = node.children[ch - 'a'];
        }
        
        return getSum(node);
    }
    
    private int getSum(TreeNode node) {
        if (node == null) {
            return 0;
        }
        
        int result = node.value;
        for (TreeNode child : node.children) {
            result += getSum(child);
        }
        
        return result;
    }
}
```

## 面试题67：最大的异或
### 题目
输入一个整数数组（每个数字都大于或者等于0），请计算其中任意两个数的异或的最大值。例如在数组[1, 3, 4, 7]中，3和4的异或结果最大，异或结果为7。

### 参考代码
``` java
static class TrieNode {
    public TrieNode[] children;

    public TrieNode () {
        children = new TrieNode[2];
    }
}

public int findMaximumXOR(int[] nums) {
    TrieNode root = buildTrie(nums);
    int max = 0;
    for (int num : nums) {
        TrieNode node = root;
        int xor = 0;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.children[1 - bit] != null) {
                xor = (xor << 1) + 1;
                node = node.children[1 - bit];
            } else {
                xor = xor << 1;
                node = node.children[bit];
            }
        }

        max = Math.max(max, xor);
    }

    return max;
}

private TrieNode buildTrie(int[] nums) {
    TrieNode root = new TrieNode();
    for (int num : nums) {
        TrieNode node = root;
        for (int i = 31; i >= 0; i--) {
            int bit = (num >> i) & 1;
            if (node.children[bit] == null) {
                node.children[bit] = new TrieNode();
            }

            node = node.children[bit];
        }
    }

    return root;
}
```
