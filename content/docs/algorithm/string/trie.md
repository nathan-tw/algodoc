---
title: Trie
bookToc: false
---



# Trie

```c++
class Trie {
public:
    class TrieNode {
        public:
            array<TrieNode*, 26> nexts;
            bool isEnd;
    };
    TrieNode* head;
    Trie() {
        head = new TrieNode();
    }
    
    void insert(string word) {
        auto curr = head;
        for(char ch: word) {
            if(curr->nexts[ch-'a'] == nullptr) {
                curr->nexts[ch-'a'] = new TrieNode();
            }
            curr = curr->nexts[ch-'a'];
        }
        curr->isEnd = true;
    }
    
    bool search(string word) {
        auto curr = head;
        for(char ch: word) {
            if(curr->nexts[ch-'a'] == nullptr) 
                return false;
            curr = curr->nexts[ch-'a'];
        }
        return curr->isEnd;
    }
    
    bool startsWith(string prefix) {
        auto curr = head;
        for(char ch: prefix) {
            if(curr->nexts[ch-'a'] == nullptr) 
                return false;
            curr = curr->nexts[ch-'a'];
        }
        return true;
    }
};
```