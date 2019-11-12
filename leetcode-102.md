#####[LeetCode-102](https://leetcode.com/problems/binary-tree-level-order-traversal/)
**Solution1:**
直接使用队列, 每次处理一层.
```java
class Solution {    // 层次遍历, 使用队列. 93.41%.
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> retvec;
        if(root == NULL) return retvec;
        queue<TreeNode*> q;
        q.push(root);
        
        int size;
        while (!q.empty()) {
            size = q.size();
            vector<int> vec;
            // 每次处理一层.
            for(int i = 0; i < size; i++, q.pop()) {
                vec.push_back(q.front()->val);
                if(q.front()->left) q.push(q.front()->left);
                if(q.front()->right) q.push(q.front()->right);
            }
            retvec.push_back(vec);
        }
        return retvec;
    }
};
```

**Solution2:**
递归, 使用 depth 记录当前处理结点所在的层数.
getorder 中语句 if (retvec.size() == depth) 表示达到新的一层.
```java
class Solution {	// 不使用队列, 递归, 记录每层的层数 depth.
					// 93.36%
public:
	vector<vector<int>> retvec;

    vector<vector<int>> levelOrder(TreeNode* root) {
        getorder(root, 0);
        return retvec;
    }

    void getorder(TreeNode* root, int depth) {
    	if(root == NULL) return;
    	if(retvec.size() == depth) {
    	// 举例: depth: 0, retvec.size(): 0  => 说明 retvec 中还没有当前层元素
    		retvec.push_back(vector<int>());
    	}
    	retvec[depth].push_back(root->val);
    	getorder(root->left, depth+1);
    	getorder(root->right, depth+1);
    }
};
```