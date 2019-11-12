##### [146. LRU Cache](https://leetcode.com/problems/lru-cache/)
题目大意: 设计并实现一个 LRU Cache.
实现对应的函数 get(key), put(key, value).
**Follow up**: 所有操作 O(1) 时间复杂度.

实现 LRU Cache 的**关键数据结构**: 双向链表 + 哈希表.
以下实现中, 定义了双向链表结点类 Node. 以及 unordered_map<int, *Node> 类型的 map 变量.

**Code 部分**
Node 结点类设计:
```java
class Node {
public:
	int key;
	int value;
	Node *pre;
	Node *next;
	Node(int key, int value) {
		this->key = key;
		this->value = value;
		this->pre = NULL;
		this->next = NULL;
	}
};
```

LRUCache 类设计:
```
LRUCache 中, 包括一个头节点 head, 和一个尾结点 tail. 均不存放数据.
一个 unordered_map 类型的变量 map 用于匹配和存储要查找的 key/<key,value>.
```
```java
class LRUCache {
private:
    int capacity;
    int count;
    unordered_map<int, Node*> map;
    // 最新, 最久.
    Node *head;
    Node *tail;

    // 移出最后一个 Node. 
    // 1. tail 不存放数据, 应移出 tail 前一个.
    // 2. 移除相关的 <key,value> 映射.
    void removenode() {
        Node *mnode = tail->pre;
        tail->pre = mnode->pre;
        mnode->pre->next = tail;
        map.erase(mnode->key);
    }

    // 1. key, value 添加新结点.
    // 2. 将该节点添加到双线链表的头部.
    // 3. 添加 <key,value> 映射.
    void addnode(int key, int value) {
        Node *adnode = new Node(key, value);

        adnode->next = head->next;
        adnode->pre = head;
        head->next->pre = adnode;
        head->next = adnode;

        map[key] = adnode;
    }

    // 将结点 node 移到双向链表头部.
    void move2head(Node *node) {
        if(node->pre != head) {
            // node 从原位置移出.
            node->pre->next = node->next;
            node->next->pre = node->pre;

            // 添加 node 到链表头部.
            node->next = head->next;
            node->pre = head;
            head->next->pre = node;
            head->next = node;
        }
    }

public:
    LRUCache(int capacity) {
        this->capacity = capacity;
        this->count = 0;
        head = new Node(-1, -1);
        tail = new Node(-1, -1);
        head->next = tail;
        tail->pre = head;
    }


    int get(int key) {
        if(map.find(key) == map.end()) {
            return -1;
        }
        Node *node = map[key];
        move2head(node);
        return node->value;
    }

    void put(int key, int value) {
    	// 1. put 之前已存在结点, 则更新数据, 并移到链表头部.
        if(map.find(key) != map.end()) {
            map[key]->value = value;
            move2head(map[key]);
        } else {
            if(count == capacity) {
                removenode();
                count--;
            }
            addnode(key, value);
            count++;
        }
    }
};
```

