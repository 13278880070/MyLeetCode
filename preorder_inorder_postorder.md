**前序遍历:**
**Code:**
```java
vector<int> inorderTraversal(TreeNode* root) {  // 中序遍历.
    vector<int> retvec;
    stack<TreeNode*> stk;

    while (root != NULL || !stk.empty()) {
        // 注意循环进行的条件.
        while (root != NULL) {
            stk.push(root);
            root = root->left;
        }
        root = stk.top();	// 取一个非空元素.
        stk.pop();
        retvec.push_back(root->val);
        root = root->right;
        // 同样的方法处理右子节点, 可能为空
    }
    return retvec;
}
```

**中序遍历:**
**Code:**
```java
vector<int> preorderTraversal(TreeNode* root) { // 前序遍历.
    vector<int> retvec;
    stack<TreeNode*> stk;

    while (root != NULL || !stk.empty()) {
        while (root != NULL) {
            retvec.push_back(root->val);
            stk.push(root);
            root = root->left;
        }
        root = stk.top();
        stk.pop();
        root = root->right;
    }
}
```

**补充: 后序遍历**
网上看到思路:
- 前序遍历 => 根, 左, 右.
- 后序遍历 => 左, 右, 根.
- 仿照前序遍历方法求解出 => 根, 右, 左.
- reverse => 得到后序遍历.


**Code:**:
```java
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        stack<TreeNode*> stk;
        vector<int> vec;
        while (root != NULL || !stk.empty()) {
            while (root != NULL) {
                vec.push_back(root->val);
                stk.push(root);
                root = root->right;
            }
            root = stk.top();
            stk.pop();
            root = root->left;
        }
        reverse(vec.begin(), vec.end());
        return vec;
    }
};
```