##### [169. Majority Element](https://leetcode.com/problems/majority-element/)
**Description**
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

You may assume that the array is non-empty and the majority element always exist in the array.

**Explain**
**169:** 给定 n 个整数, 找到出现次数 > ⌊ n/2 ⌋ 的数.
假设: 给定整数组非空, 并且**总是存在返回值**

**Example**
```
Input: [3,2,3]
Output: 3
```

**Solution**
&emsp;&emsp;使用两个整数: `candidate`, `count`. 其中 `candidate` 用于记录当前待"选举"元素, `count` 记录当前待"选举"元素 `candidate` 的"票数".

&emsp;&emsp;遍历所有元素, 对于每个元素 `ele` , 遵循以下规则:

1. 如果 `count` 为零, 则将 `candidate` 设置为 `ele`

2. 如果 `ele == candidate`, 则将 `count` + 1

3. 否则, `count` - 1

&emsp;&emsp;最后, 返回 `candidate` 即为所求的元素.(这里题目中假设一定存在一个返回值, 直接返回 `candidate` 即可)

例子:

对于整数集合: 2, 2, 4, 2, 3

`candidate`: 2, 2, 2, 2, 2
`count`: &emsp;&emsp;1, 2, 1, 2, 1

(这里的 `candiate` 和 `count` 是对当前元素执行完一次循环之后的值)

**Code**
```java
class Solution {    // Boyer-Moore Algorithm
public:
    int majorityElement(vector<int>& nums) {
        int count = 0, candidate = 0;

        for (int ele : nums) {
            if(count == 0) candidate = ele;
            if(ele == candidate) count++;
            else count--;
        }
        return candidate;
    }
};
```

<br>
##### [229. Majority Element II](https://leetcode.com/problems/majority-element-ii/)

**Description**
Given an integer array of size n, find all elements that appear more than ⌊ n/3 ⌋ times.

Note: The algorithm should run in linear time and in O(1) space.

**Explain**
**229: **给定 n 个整数, 找到所有出现次数 > ⌊ n/3 ⌋ 的数, 并返回.
**返回结果可能为空**

**Example1**
```
Input: [3,2,3]
Output: [3]
```

**Example2**
```
Input: [1,1,1,3,3,2,2,2]
Output: [1,2]
```

**Solution**
&emsp;&emsp;借助`169`题的思想, 用两个"选举人" `cant1` 和 `cant2`, 以及两个计数值 `num1`, `num2`

&emsp;&emsp;遍历所有元素, 对于每个元素 `ele`, 遵循以下规则:

1. 如果 `num1 == 0` 且 `ele != cant2`, 则将 `cant1` 置为 `ele`

2. 否则, 如果 `num2 == 0` 且 `ele != cant1`, 则将 `cant2` 置为 `ele`
(以上两步的目的是判断是否可以将 `ele` 作为 `cant1` 或 `cant2`, 与 LeetCode.169 中的第一步是类似作用)

3. 如果 `ele == cant1` 则 `num1++`

4. 否则, 如果 `ele == cant2` 则 `num2++`

5. 否则, `num1--`, `num2--`


&emsp;&emsp;最终, 相比于 `169` 题, 题目中没有假设一定有解, 因此, 还要检测 `cant1` 和 `cant2` 是否为满足条件 `> ⌊ n/3 ⌋`, 因此, 遍历计数 `cant1`, `cant2`

**Code**
```java
class Solution {	// 共 n 个数, 返回出现次数 > n/3 的元素.
public:				// 线性时间, O(1) 空间
    vector<int> majorityElement(vector<int>& nums) {
        int len = nums.size();

        // cant1, cant2
        int cant1, cant2, num1 = 0, num2 = 0;

        for (int ele : nums) {
        	if(!num1 && ele != cant2) cant1 = ele;
        	else if(!num2 && ele != cant1) cant2 = ele;

        	if(ele == cant1) num1++;
        	else if(ele == cant2) num2++;
        	else {
        		num1--;
        		num2--;
        	}
        }

        num1 = 0, num2 = 0;
        for (int ele : nums) {
        	if(ele == cant1) num1++;
        	if(ele == cant2) num2++;
        }

        vector<int> retvec;

        if(num1 > len/3) retvec.push_back(cant1);
        if(num2 > len/3) retvec.push_back(cant2);

        return retvec;
    }
};
```

###### 一个比较好的解释: [My understanding of Boyer-Moore Majority Vote](https://leetcode.com/problems/majority-element-ii/discuss/63537/My-understanding-of-Boyer-Moore-Majority-Vote)

###### 参考: [Majority Voting Algorithm](https://gregable.com/2013/10/majority-vote-algorithm-find-majority.html)
