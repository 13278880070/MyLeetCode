

**Description**
Given a non negative integer number num. For every numbers i in the range 0 ≤ i ≤ num calculate the number of 1's in their binary representation and return them as an array.
(返回 0 ~ num 所有数的 1 的位数)

**Example1**
```
Input: 2
Output: [0,1,1]
```

**Example2**
```
Input: 5
Output: [0,1,1,2,1,2]
```
<br>
**Solution1**
```java
class Solution {	// 0 ~ num 中 1 的个数
public:
    vector<int> countBits(int num) {
        vector<int> dp(num+1, 0);
        dp[0] = 0;

        int x;

        for (int i = 1; i <= num; i++) {
        	if (i & 1) {	// 奇数
        		dp[i] = dp[i-1] + 1;
        	} else {		// 偶数
        		x = ~(i-1);
        		// 取倒数第一个 0

        		x = log2(x & -x);
        		dp[i] = dp[i-1] - (x-1);
        	}
        }

        return dp;
    }
};
```

1. 对于奇数, dp[i] 为 dp[i-1] + 1

2. 对于偶数, 首先通过 x = ~(i-1) 运算对 (i-1) 的所有位取反, 运算 (x & -x) 得到 i-1 的最后一个 "0", 通过 log2() 算出 i-1 的所有位中在最后一个 "0" 之后的 "1" 的个数 count.   dp[i] 位 dp[i-1] - (count - 1).


**Solution2**
```java
class Solution {
public:
    vector<int> countBits(int num) {
        vector<int> dp(num+1);
        dp[0] = 0;

        // i & (i-1)  =>  将最后一个 1 置 0
        // 因此, i 中 1 的个数等于上式 + 1.
        for (int i = 1; i <= num; i++) {
        	dp[i] = dp[i & (i-1)] + 1;
        }

        return dp;
    }
};
```

运算 `i & (i-1)` 将 i 中的最后一个 "1" 置 0。


**总结**

`x & (x - 1)` 将 x 的最后一个 "1" 置 0.

`x & (-x)` 得到 x 的最后一个 "1".
