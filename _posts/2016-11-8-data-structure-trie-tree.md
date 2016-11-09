---
layout: post
title: Data structure - trie tree
categories: [blog ]
tags: [interview, data structure]
description: Data structure - trie tree
---


##Concept

we have key words: *and,as,at,cn,com*，how can we contruct a trie tree？

####From the graph above we can tell:
1. the root node do not contain any characters. It is a dummy node similar with the one in linked list, only used as the root of the whole tree. 
2. we store current char at current level of trie tree. 
3. use an boolean to note that whether current chars is a word. 

#### when do we use trie tree
Trie tree can do anything that hashmap can do. The difference is that it save words by letters. So we can save same space when there have many words with same prefix. 

So when we need to counting/ macthing prefix and or we want to save some space. we use trie tree.

## Related Problems
####Implement Trie
Implement a trie with insert, search, and startsWith methods.

1. we can implement it with hashmap. In insert function and search function we can use for loop function which is defined in trie class.


```
class TrieNode {
    // Initialize your data structure here.
    HashMap<Character, TrieNode> children = new HashMap<Character, TrieNode>();
    char c;
    boolean isLeaf;
    public TrieNode() {
    }
    public TrieNode(char c) {
        this.c = c;
    }
} 

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    public void insert(String word) {
        
        int len = word.length();
        TrieNode currentNode = root;
        HashMap<Character, TrieNode> currentChild = root.children;
        char[] sArray = word.toCharArray();

        for (int i = 0; i < len; i++) {
            char wc = sArray[i];
            if (currentChild.containsKey(wc)) {
                currentNode = currentChild.get(wc);
                currentChild = currentNode.children;
            } else {
                TrieNode temp = new TrieNode(wc);
                currentChild.put(wc, temp);
                currentNode = temp;
                currentChild = temp.children;
            }
            if (i == len - 1) {
                currentNode.isLeaf = true;
            }
        }
    }
    

    // Returns if the word is in the trie.
    public boolean search(String word) {
        TrieNode temp = searchWordNodePos(word);
        if (temp == null) {
            return false;
        } else {
            return temp.isLeaf;
        }
    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    public boolean startsWith(String prefix) {
        return searchWordNodePos(prefix) != null;
    }
    
    private TrieNode searchWordNodePos(String s){
        HashMap<Character, TrieNode> children = root.children;
        TrieNode cur = null;
        char[] sArray = s.toCharArray();
        for(int i = 0; i < sArray.length; i++){
            char c = sArray[i];
            if(children.containsKey(c)){
                cur = children.get(c);
                children = cur.children;
            } else{
                return null;
            }
        }
        return cur;
    }
}

// Your Trie object will be instantiated and called as such:
// Trie trie = new Trie();
// trie.insert("somestring");
// trie.search("key");
```

2. trie trie is a tree with branching factor equal to 26 (26叉树), so we can use array to represent all the children

the fucntion can write as recursion, which can make the code clean.

the recursion function need to be definded in the trieNode class with index. **note the difference of write recursion in class defination. We need to use the key word this which means the trieNode object itself**
（类里面定义函数递归，多写几遍）

*this是指当前对象自己。 
当在一个类中要明确指出使用对象自己的的变量或函数时就应该加上this引用.*

#### word search II

Given a 2D board and a list of words from the dictionary, find all words in the board.

Each word must be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once in a word.

```
For example,
Given words = ["oath","pea","eat","rain"] and board =
[
  ['o','a', 'a', 'n'],
  ['e','t','a','e'],
  ['i','h','k','r'],
  ['i','f','l','v']
]

Return ["eat","oath"].

```

 暴力方法对于给定的words array 每一个单词做dfs， 优化方法是在word search 的基础上建立字典树，把dfs过程中找到的结果放倒result array里面去。

优化方法是在word search 的基础上利用字典树。 

- 首先根据字典里的单词建立trie tree。
- 然后和word search 一样进行dfs回溯查找，如果当前的字母不在字典树里面就不需要进行dfs向下查找。如果存在字典树里面，就一直向下查找。
- 如何来判断单词结尾，对于常规的字典树，是在叶子节点存一个isword的boolean变量来表示。这道题也是类似的方法，在做insert的同时，判断如果是最后一个字母的时候，在节点上做一个标记。**但是注意，和实现字典树不用，并不是存一个boolean值，而是把整个word string 存下来，这样我们走到叶子节点的时候就知道当前叶子所对应的单词是什么。就可以把相应的单词存到result里面去。**

注意在实现字典树搜索的时候，以前是把search 定义在trie tree的类里面利用for loop查找／ 或者在trie tree类里面的search函数叫trie node里面定义的递归版的带index的search函数。 这道题不需要在类里面定义search，而是在进行dfs的同时，记住当前的tree node节点。这样就能做到每往下走一层，在当前的trie树的子节点里面找对应的单词，而不需要从根节点来找对应的string（重要的区别！！！）。另外有个小技巧，不需要额外开辟数组记住visited[][] 直接更新board[][]把特定的位置变成特定的flag值，题目里面我用了‘0’ 作为特定值，表示visited过。记住，从递归体退出的时候，要把修改过的board给改回来。
##### 代码

```
public class Solution {
    class TrieNode {
        public TrieNode[] children;
        public String s;   
    
        TrieNode () {
            this.children = new TrieNode[26];
            this.s = "";
        }
        public void insert(String word, int index) {
            if (word.length() == index) {
                this.s = word;
                return;
            }
            int pos = word.charAt(index) - 'a';
            if (children[pos] == null) {
                children[pos] = new TrieNode();
            }
            children[pos].insert(word, index + 1);
        }
    }    
     
    class TrieTree {
        TrieNode root;
        public TrieTree() {
            root = new TrieNode();
        }
        public void insert(String word) {
            root.insert(word, 0);
        }
    }
    
    private ArrayList<String> res = new ArrayList<String>();
    private int[] offset = {0, 1, 0, -1, 0};
    private int m, n;
    public List<String> findWords(char[][] board, String[] words) {
        TrieTree tree = new TrieTree();
        for (String word : words) {
            tree.insert(word);
        }
   
        m = board.length;  
        n = board[0].length;
        for (int i = 0; i < m; i++) {  
            for (int j = 0; j < n; j++) {  
                dfs(board, i, j, tree.root); 
            }  
        }  
        return res;  
    }
    
    public void dfs(char[][] board,  int i, int j, TrieNode node) {
        
        if(node == null) {
            return;
        }
        if (node.s != "" &&  !res.contains(node.s) ) {
            res.add(node.s);
        }
        if (i < 0 || i >= m || j < 0 || j >= n){
            return;
        }
        
        int pos = board[i][j] - 'a';
        if(board[i][j] != '0' && node.children[pos] != null){
            for (int k = 0; k < 4; k++) {
                char now = board[i][j];
                board[i][j] = '0';
                dfs(board, i + offset[k], j + offset[k + 1], node.children[pos]);
                board[i][j] = now;
            }
        }
    }    
}
```
  
    

word search ii
犯过的错误：

1. 注意这3个if的顺序，如果检测边界条件在增加res之前，那么对于只有一个节点的输入，将得到空的result。 

2. 把 m，n作为全局变量的话 在class 里面定义了以后，直接赋值，如果在方法里面又重新定义了 int m =.. 这样会造成方法里访问的是这个局部的m。

3. 在遍历board做递归的时候，是在循环里面找到新的 i j 然后在下一层递归里面处理相应的结果。注意分清递归层，出口，递归体。

 
    
