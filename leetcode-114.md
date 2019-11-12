**题目链接:**
[LeetCode-114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)

**Solution1**
递归处理, 对于每个结点, 每次处理返回其**右子树的最右结点**:
1. 处理该结点的左子树, 返回结点 left.
2. 处理该结点的右子树, 返回结点 right.
3. 根据 left, right 将左子树连接到该结点与右子树的中间.
4. 根据 left, right 是否为空, 返回 left / right.

**Code: **
```java
class Solution {
public:
    void flatten(TreeNode* root) {
    	flattenit(root);
    }

    // 每次处理, 返回当前处理结点的右子树的最右结点.
    TreeNode* flattenit(TreeNode* root) {
    	if(root == NULL) return NULL;
    	if(root->left == NULL && root->right == NULL) return root;
        // 为空或为叶子结点, 直接返回.

    	TreeNode* r_ret = flattenit(root->right);
    	TreeNode* l_ret = flattenit(root->left);
    	if(root->left == NULL) return r_ret;
        // 左子树为空, 右子树处理完成, 直接返回 r_ret.

    	TreeNode* r_child = root->right;
    	root->right = root->left;
    	root->left = NULL;
        l_ret->right = r_child;
        // 将左子树连接到 根节点与右子树之间.

    	return r_ret == NULL ? l_ret:r_ret;
        // 根据右子树是否为空, 返回 l_ret / r_ret.
    }
};
```

**Solution2**
非递归做法.
1. 将未经处理的左子树连接到 未经处理的右子树和根节点 的中间.
2. 继续处理 root->right.

**Code:**
```java
Solution {    // 非递归, 从上往下依次处理.
public:
    void flatten(TreeNode* root) {
        while (root != NULL) {
            if(root->left != NULL) {
                TreeNode* pre = root->left;
                while (pre->right != NULL) {
                    pre = pre->right;
                }
                // 找到左子树的最右结点.
                pre->right = root->right;
                root->right = root->left;
                root->left = NULL;
                // 左子树连接到 右子树和根节点的中间 完成.
            }
            root = root->right;
            // 继续处理 当前节点的右子结点.
        }
    }
};
```

