##### [264. Ugly Number II](https://leetcode.com/problems/ugly-number-ii/)

**Description**
```
Write a program to find the n-th ugly number.

Ugly numbers are positive numbers whose prime factors only include 2, 3, 5.
```

**Example**
```
Input: n = 10
Output: 12
Explanation: 1, 2, 3, 4, 5, 6, 8, 9, 10, 12 is the sequence of the first 10 ugly numbers.
```

**Solution**
&emsp;&emsp;题目要求: 要返回第 n 位丑数, 1 视为丑数。

**Code**
```java
class Solution {
public:
    int nthUglyNumber(int n) {
        if (n <= 6) return n;

        vector<int> dp(n);
        dp[0] = 1;

        int t2 = 0, t3 = 0, t5 = 0;

        for(int i = 1; i < n; i++) {
            dp[i] = min(dp[t2]*2, min(dp[t3]*3, dp[t5]*5));

            if(dp[i] == dp[t2]*2) t2++;
            if(dp[i] == dp[t3]*3) t3++;
            if(dp[i] == dp[t5]*5) t5++;
        }

        return dp[n-1];
    }
};
```

**Graph**

下标 | dp | t2 | t3 | t5
--- | --- | --- | --- | ---
0 | 1 | 0 | 0 | 0
1 | 2 | 1 | 0 | 0
2 | 3 | 1 | 1 | 0
3 | 4 | 2 | 1 | 0
4 | 5 | 2 | 1 | 1
5 | 6 | 3 | 2 | 1
6 | 8 | 4 | 2 | 1
7 | 9 | 4 | 3 | 1
8 | 10 | 5 | 3 | 2

```
t2, t3, t5 用于计算出 dp[t2], dp[t3], dp[t5]。
并将每个 dp[ti] 与 i 相乘, 取其中最小值。

每次将 2, 3, 5 与之前已经生成的 Ugly Number 进行相乘。 t2, t3, t5 用于索引对应之前的 Ugly Number。
```
