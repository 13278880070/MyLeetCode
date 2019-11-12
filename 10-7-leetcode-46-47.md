####题目链接
#####[Leetcode-46](https://leetcode.com/problems/permutations/)

**Description: **
给定一组无重复整数, 返回所有可能的排列.

**Example:**
```
Input: [1,2,3]
Output:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

**解法一:**
```java
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {		// 使用一个 used[] 向量来记录是否被使用
        int len = nums.size();
        vector<bool> used(len, false);
        vector<vector<int>> retvec;
        vector<int> advec;
        getpermute(nums, advec, used, retvec, 0);
        return retvec;
    }

    void getpermute(vector<int>& nums, vector<int>& advec, vector<bool>& used, vector<vector<int>>& retvec, int sum) {
    	if(sum == nums.size()) {
    		retvec.push_back(advec);
    		return ;
    	}
    	for(int i = 0; i < nums.size(); i++) {
    		if(used[i] == false) {
    			used[i] = true;
    			advec.push_back(nums[i]);
    			getpermute(nums, advec, used, retvec, sum+1);
    			used[i] = false;
    			advec.pop_back();
    		}
    	}
    }
};
```
使用一个向量 used 来记录每个整数是否已被使用, 直接回溯得到最后的结果.

**解法二:**
```java
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {	// swap(nums[i], nums[j])...
        vector<vector<int>> retvec;
        getpermute(nums, retvec, 0);
        return retvec;
    }

    void getpermute(vector<int>& nums, vector<vector<int>>& retvec, int start) {
    	if(start == nums.size() - 1) {
    		retvec.push_back(nums);
    		return;
    	}
    	for(int i = start; i < nums.size(); i++) {	// 当前的树向下扩展时, 考虑接下来的第一个元素
    		swap(nums[start], nums[i]);
    		getpermute(nums, retvec, start);
    		swap(nums[start], nums[i]);
    	}
    }
};
```
for 循环考虑的是当前向下扩展的第一个结点的取值. 注意这里 swap 交换之后, 要交换回去.

#####[Leetcode-47](https://leetcode.com/problems/permutations-ii/)

**Description**
给定一组有重复的整数, 返回所有可能的**唯一**的排列.

**Example:**
```
Input: [1,1,2]
Output:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

**AC代码:**
```java
class Solution {
public:
    void recursion(vector<int> num, int i, int j, vector<vector<int> > &res) {
        if (i == j-1) {
            res.push_back(num);
            return;
        }
        for (int k = i; k < j; k++) {
            if (i != k && num[i] == num[k]) continue;
            swap(num[i], num[k]);
            recursion(num, i+1, j, res);
        }
    }
    vector<vector<int> > permuteUnique(vector<int> &num) {
        sort(num.begin(), num.end());
        vector<vector<int> >res;
        recursion(num, 0, num.size(), res);
        return res;
    }
};
```
```
这里需要注意的是, for 循环内部为什么不需要 swap 回去?
在 permuteUnique() 函数中, 先对 num 进行了 sort 排序.
这样, 重复的整数都会是相邻的, 整数为从小到大排列.

考虑整数集合 1, 1, 2, 2, 2, 4
现在只考虑第一个扩展结点:
  (1) num[0] 考虑在内, 符合要求
  (2) num[1] = num[0], continue 跳过
  (3) 交换 num[0] 与 num[2] 整数集合变为
  		2, 1, 1, 2, 2, 4.
  (4)(5) num[3], num[4] = num[0], continue 跳过
  (6) 交换 num[0] 与 num[5] 整数集合变为
        4, 1, 1, 2, 2, 2.

上述过程的正确执行, 还有一个条件就是代码中 num 参数的传递.
可以看到, num 的传递为值传递, 所以, 向下继续扩展结点的时候, 并不会改变上一个扩展结点的时候的整数的排序情况.
因此, 上述过程得以正确执行.

可以看到, 我们之所以不 swap 两次, 目的就是让"下一组"要判断的整数位于第一位.
将 1 置于第一位, 则 continue 跳过接下来的其他 1
将 2 置于第一位, 则 continue 跳过接下来的其他 2
4, ....
每一次的 swap ,都是确保位于当前最前面的一位的值的唯一性.
num 的值传递, 而不是引用传递也确保了 执行的正确性.
```
