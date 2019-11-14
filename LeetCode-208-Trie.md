##### [LeetCode.208](https://leetcode.com/problems/implement-trie-prefix-tree/submissions/)

**实现一个字典树 Trie**
[Trie: 百度百科](https://baike.baidu.com/item/%E5%AD%97%E5%85%B8%E6%A0%91/9825209?fromtitle=Trie&fromid=140945&fr=aladdin)

###### 结点类型的定义
在我的实现中, 用了两种不太一样的结点类型.

**Node1**
视频教程地址: [Trie Data Structure (EXPLAINED)](https://www.youtube.com/watch?v=-urNrIAQnNo)
(演示不断插入每个字符之后树的变化)

两个成员变量:

1. map, 用于接受一个字符, 并得到对应的下一个孩子结点

2. isLeaf, 指定当前是否到达叶子结点

除此之外, 还定义了一些自带的函数
```java
class Node
{
public:
	unordered_map<char, Node*> map;
	bool isLeaf;

	Node() {
		this->isLeaf = false;
	}

	bool findchar(char ch) {
		return map.find(ch) != map.end();
	}

	void admap(char ch, Node *n) {
		map[ch] = n;
	}

	Node* getNext(char ch) {
		return map[ch];
	}
};
```

**Node2**
根据 LeetCode.208 需要存储每个单词, 并且单词都是小写, 因此, 定义如下成员变量:

1. Node* child[26], 初始化为 NULL. 26 个字符对应 26 个孩子结点, 根据当前字符 ch 找到对应的孩子结点

2. isLeaf, 指示当前是否为叶子结点.

```java
class Node
{
public:
    bool isLeaf;
    Node* child[26];    // 26 个字母, 使用 26 个孩子结点指针.

    Node() {
        this->isLeaf = false;
        for(int i = 0; i < 26; i++) child[i] = NULL;
    }

};
```

**Node1 对应 Trie 的实现**

```java
class Trie_DT
{
private:
	Node *root;
public:
	Trie_DT() {
		root = new Node();
	}
	~Trie_DT() {}

	// 不断根据当前字符 ch, 找到或添加下一个对应的孩子结点
	void insert(string word) {
		Node *node = root;
		for(char ch : word) {
			if(!node->findchar(ch)) {
				Node *adnode = new Node();
				node->admap(ch, adnode);
			}
			node = node->getNext(ch);
		}
		node->isLeaf = true;
	}

	// 根据当前字符 ch 一直索引孩子结点
	bool startwith(string prefix) {
		Node *node = root;
		for(char ch : prefix) {
			if(!node->findchar(ch)) return false;
			node = node->getNext(ch);
		}
		return true;
	}

	// 与 startwith() 唯一区别就是判断是否达到的叶子结点
	bool search(string word) {
		Node *node = root;
		for(char ch : word) {
			if(!node->findchar(ch)) return false;
			node = node->getNext(ch);
		}
		return node->isLeaf;
	}
};
```

**Node2 对应 Trie 的实现**
```java
class Trie {
private:
    Node* root;
public:
    /** Initialize your data structure here. */
    Trie() {
        root = new Node();
    }

    /** Inserts a word into the trie. */
    void insert(string word) {
        Node* node = root;
        int index;
        for(char ch : word) {
            index = ch - 'a';
            if(!node->child[index]) {
                node->child[index] = new Node();
            }
            node = node->child[index];
        }
        node->isLeaf = true;
    }

    /** Returns if the word is in the trie. */
    bool search(string word) {
        Node* node = root;
        int index;
        for(char ch : word) {
            index = ch - 'a';
            if(!node->child[index]) return false;
            node = node->child[index];
        }
        return node->isLeaf;
    }

    /** Returns if there is any word in the trie that starts with the given prefix. */
    bool startsWith(string prefix) {
        Node* node = root;
        int index;
        for(char ch : prefix) {
            index = ch - 'a';
            if(!node->child[index]) return false;
            node = node->child[index];
        }
        return true;
    }
};
```

**关于叶子结点和孩子结点索引**
视频截图:
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);"
    src="https://raw.githubusercontent.com/wiki/13278880070/PIC/11-14-pic1.png">
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">插入单词"car", "done"</div>
</center>

```
插入单词 "car", "done" 之后的树的截图.
例子:
查找"car":
	1. 根节点 root, 根据第一个字符 "c", 索引到对应的结点 node1(图中带'a'的结点)
	2. node1, 根据第二个字符 "a", 索引到对应的结点 node2(图中带'r'的结点)
	3. node2, 根据第三个字符 "r", 索引到对应的结点 node3(左下角的元素为空的结点)
	4. node3 的 isLeaf 为 true, 说明当前单词 "car" 存在.

考虑添加单词"Do":
	1. 根节点 root, 根据第一个字符 "D", 索引得到对应结点 node1(带'o'的结点)
	2. node1, 根据第二个字符 "o", 索引得到对应结点 node2(带'n'的结点)
	3. 字符全部处理完成, 则将当前结点的 isLeaf 设置为 true, 即 node2 的 isLeaf 为true

查找"Do":
	根据前面规则进行索引, 最后将得到为 isLeaf 为 true 的'n' 结点, 并返回查找结果 true
```