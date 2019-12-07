##### [215. Kth Largest Element in an Array](https://leetcode.com/problems/kth-largest-element-in-an-array/)

**Description**
&emsp;&emsp;Find the kth largest element in an unsorted array. Note that it is the kth largest element in the sorted order, not the kth distinct element.

**Example**
```
Input: [3,2,1,5,6,4] and k = 2
Output: 5
```

**Solution**

**STL**

`nth_element`
```java
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        auto res = nums.begin() + k - 1;

		// 默认从小到大的顺序.
        // greater, 按从大到小的顺序.
        nth_element(nums.begin(), res, nums.end(), greater<int>());

        return *res;
    }
};

nth_element() 表示从 first 开始, 到 last(不包括 last)。 将其中的元素根据第 kth 个元素对数组进行操作, 使位于 kth 前面的元素均 < kth, 位于 kth 后面的元素均 > kth。
```

`partial_sort`

```java
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        partial_sort(nums.begin(), nums.begin() + k, nums.end(), greater<int>());
        return nums[k - 1];
    }
};
```

`priority_queue`

```java
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
    	// 最大堆
        priority_queue<int, vector<int>, greater<int>> q;

		for (int ele : nums) {
        	q.push(ele);
            if (q.size() > k) q.pop();
        }

        return q.top();
    }
};

```


<br>
&emsp;&emsp;使用 `quick_sort` 中类似的思想, 使用一个数 x 来对整个数组进行 分割, 使得 x 左边的数均 < x, x 右边的数均 > x。

```java
class Solution {	// 找到第 k 大的数.
public:				// My Solution: 利用快速排序过程中的下标
	int ret;
    int findKthLargest(vector<int>& nums, int k) {
        int len = nums.size();
        return findKth(nums, 0, len-1, k-1);
    }

    // partition.
    int findKth(vector<int>& nums, int l, int r, int k) {
    	int x = nums[l];
    	int rl = l;
    	int rr = r;
    	while (l < r) {
    		while (l < rr && nums[l] >= x) l++;
    		while (r > rl && nums[r] < x) r--;
    		if (l > r) break;
    		swap(nums[l], nums[r]);
    	}
    	swap(nums[rl], nums[r]);
    	int ret;
    	if(r < k) {
    		ret = findKth(nums, l, rr, k);
    	} else if(r > k) {
    		ret = findKth(nums, rl, r-1, k);
    	} else ret = nums[r];
    	return ret;
    }
};
```


