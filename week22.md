<!--
 * @Description: 
 * @Versions: 
 * @Author: Vernon Cui
 * @Github: https://github.com/vernon97
 * @Date: 2021-01-09 01:22:48
 * @LastEditors: Vernon Cui
 * @LastEditTime: 2021-01-09 01:24:57
 * @FilePath: /.leetcode/Users/vernon/Leetcode-notes/week22.md
-->
# Week 22 - Leetcode 211 - 220

#### 211 - 添加与搜索单词 - 数据结构设计

这题就是在Trie树的搜索结果上改一改，把通配符'.' 的搜索考虑进去；
由于通配符的存在，搜索改成递归形式的比较好一些；

```cpp
class WordDictionary {
public:
    struct Node
    {
        Node* son[26];
        bool is_end;
        Node()
        {
            is_end = false;
            for(int i = 0; i < 26; i++)
                son[i] = nullptr;
        }
    }*root;
    /** Initialize your data structure here. */
    WordDictionary() {
        root = new Node();
    }
    
    /** Adds a word into the data structure. */
    void addWord(string word) {
        auto p = root;
        for(auto c : word)
        {
            int u = c - 'a';
            if(!p->son[u]) p->son[u] = new Node();
            p = p->son[u];
        }
        p->is_end = true;
    }

    bool find_word(Node* p, string& word, int si)
    {
        if(si == word.size()) return p->is_end;
        if(word[si] == '.')
        {
            for(int i = 0; i < 26; i++)
                if(p->son[i] && find_word(p->son[i], word, si + 1))
                    return true;
        }
        else
        {    
            int u = word[si] - 'a';
            if(p->son[u]) return find_word(p->son[u], word, si + 1);   
            else return false;
        }
        return false;
    }
    
    /** Returns if the word is in the data structure. A word could contain the dot character '.' to represent any one letter. */
    bool search(string word) {
        return find_word(root, word, 0);
    }
};
```