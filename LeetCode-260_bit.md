##### [LeetCode-260.Single Number III](https://leetcode.com/problems/single-number-iii/)

**Description**
```
Given an array of numbers nums, in which exactly two elements appear only once and all the other elements appear exactly twice. Find the two elements that appear only once.
```

**Example**
```
Input:  [1,2,1,3,2,5]
Output: [3,5]
```

**Note**
1. The order of the result is not important. 

2. `linear` runtime complexity. only `constant space complexity`


**Solution**

&emsp;&emsp; 之前做过这个 `Single Number` 系列的前两个版本: [LeetCode- 137.Single Number II (位运算)](https://blog.csdn.net/hudaJY/article/details/102870053), 并提供了使用位运算的解法.

&emsp;&emsp; 与之前解法不同, 该题 `Single Number III` 中最终的解不止一个, 而是有两个不同的解。

&emsp;&emsp; 首先想到的应该与前两个系列的题一样的思路, 使用运算符 `^`, `&` 用 x1, x2... 等来保存每个数出现的次数等思想。 但是该题最终有两个结果, 如 `Example` 中的 `3, 5`, 这样, 直接使用异或操作 `x ^= nums[i]` 之后, 最终 `x` 中存的是 `3` 和 `5` 两个数的异或结果。

&emsp;&emsp; 参考别人的思路, 这里要想办法**将所有数划分为两类**, 一类包含 `3`, 另一类包含 `5`, 并且相同数将在同一类中。 使用 `a` 来 `^` 第一类, 使用 `b` 来 `^` 第二类, 最终得到的结果中, `a` 就是第一个要求的结果, `b` 就是第二个要求的结果。 返回 `a, b` 就是最终要求的两个数。

&emsp;&emsp; 具体的划分规则, 这里采用的是利用 `x` 的**倒数第一个 `1` **来划分(注意 `x` 是所有数 `^` 的结果)。 利用位运算 `(x & (x-1)) ^ x` 即可得到 `x` 的最后一个 `1`, 使用变量 `lastbit` 记录。  其中重要的一条是: `x` 的最后一个 `1` 只能是由 `3` 或 `5` 中的一个数提供的, 要么是 `3`, 要么是 `5` (异或操作)。

&emsp;&emsp; 最终, 再次遍历所有元素, 将每个元素 `ele` 与 `lastbit` 进行 `&` 运算, 根据 `&` 运算结果将所有数划分为两类, 分别进行 `^` 运算。

**Code**
```java
class Solution {
public:
    vector<int> singleNumber(vector<int>& nums) {
        int x = 0, y;
        vector<int> retvec;
        for(int ele : nums) x ^= ele;
        
        // 取 x 的最后一位
        int lastbit = (x & (x - 1)) ^ x;
        
        // lastbit 只可能由最后结果 a, b 中的一个提供
        // 因此, 以 lastbit 为依据, 将 a, b 划分到不同组, 并且同组中出现过两次数的都将变为零
        
        int a = 0, b = 0;
        for(int ele : nums) {
            if(ele & lastbit) a ^= ele;
            else b ^= ele;
        }
        
        return vector<int>{a, b};
    }
};
```
