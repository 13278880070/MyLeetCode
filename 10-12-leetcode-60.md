#### Leetcode-60
**链接: [60.Permutation Sequence](https://leetcode.com/problems/permutation-sequence/)**

**Description:**
```
The set [1,2,3,...,n] contains a total of n! unique permutations.

By listing and labeling all of the permutations in order, we get the following sequence for n = 3:
	1."123"
	2."132"
	3."213"
	4."231"
	5."312"
	6."321"

Given n and k, return the kth permutation sequence.
```
**Note:**
- Given n will be between 1 and 9 inclusive.
- Given k will be between 1 and n! inclusive.

给定 n 和 k, 返回其中的第 k 个排列.

本来想着像 [46.Permutations](https://leetcode.com/problems/permutations/) 类似的方法去做, 然后搞一个 count 计算到达第多少个排列就行, 结果发现并不是严格按照大小来遍历的.

记录以下别人的解法:
**Code: **
```java
class Solution {
public:
    string getPermutation(int n, int k) {
        string str(n, '0');
        int fac = 1;
        for(int i = 1; i <= n; i++) {
        	str[i-1] += i;
        	fac *= i;
        }
        // 构造 字符串 "123...n" 和 整数值 1*2*...*n

        k--;
        // 根据目标字符串与原字符串的差值来计算.
        // 目标字符串为第 k 位, 差值为 k-1.
        int i, j;
        for(i = 0; i < n; i++) {
	        fac /= (n-i);
	        j = i + k/fac;
            // calculate index of char to put at s[i]
	        char ch = str[j];
	        // remove ch by shifting to cover up (adjust the right part).
	        for(; j > i; j--) str[j] = str[j-1];
	        k %= fac;
	        str[i] = ch;
	    }
	    return str;
    }
};
```
`k--`下面的第一个 for 循环, 目的是计算目标字符 s[i] 的值.
例子:
```
原字符串: "1234"

目标字符串: "3124"
现在, 我们反过来求目标字符串为 第多少位.
目标字符串中, 字符'3'被 "左移2位"(不是交换) 到了第一位
=> 得到目标字符串与原字符串的差: 2*3!.  =>  第 2*3!+1 = 13 位

目标字符串: "3214"
1. '3'被左移两位 => '3124'
2. '2'被左移一位 => '3214'
差值: 2*3! + 1*2! => 第 2*3! + 1*2! + 1 = 15位.

关于阶乘: 某个数左移 x 位后, 该数后面的位数即为"阶乘数".
		  x * 阶乘数 => 差值.
```

原 Leetcode-60 的解法, 就是上述的逆过程。