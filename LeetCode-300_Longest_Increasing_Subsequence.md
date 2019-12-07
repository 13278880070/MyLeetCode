##### [LeetCode-300. Longest Increasing Subsequence](https://leetcode.com/problems/longest-increasing-subsequence/)

**Description**
```
Given an unsorted array of integers, find the length of longest increasing subsequence.
```

**Solution**
```
DP, 动态规划求解最长增长序列, vector<int> dp。

对于数组中的每个元素 ele, 执行以下规则:

1. 如果 ele > dp[size-1] || !size, 即当前元素 ele 大于当前 dp 中存储元素的最后一个, 或者当前 dp 中元素为空, 则将 ele 添加到 dp 中。

2. 如果 ele < dp[size-1], 即当前元素 ele 小于当前 dp 中存储元素的最后一个, 则 使用-二分查找-找到小于 ele 的元素的前一个元素 x, 使用 ele 替换 x 即可。

3. 最终返回的最长增长子序列即为 dp 长度。

```

**Code**

```java
class Solution {	// 最长增长序列 (dp, 二分查找)
public:
    int lengthOfLIS(vector<int>& nums) {
        int len = nums.size();
        if(!len) return 0;

        int ret = 0, size = 0, l, r, mid;

        vector<int> dp(len);

        for (int i = 0; i < len; i++) {
        	if (!size || nums[i] > dp[size-1]) dp[size++] = nums[i];
        	else if(nums[i] < dp[size-1]) {
        		// 使用二分查找找到插入的位置

        		l = 0, r = size-1;

        		while (l < r) {
        			mid = (l + r) >> 1;
        			if(dp[mid] > nums[i]) r = mid;
        			else if(dp[mid] < nums[i]) l = mid + 1;
        			else break;
        		}

        		if (l >= r) dp[r] = nums[i];
        	}
        }

        return size;
    }
};
```
