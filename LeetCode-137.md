#### LeetCode-137
**链接: [137. Single Number II](https://leetcode.com/problems/single-number-ii/)**

**Solution1:**
不合题意的解法.
1. 循环遍历每个数的每一位(32 bit). 
2. 将所有数的某一位 (第 i 位) 加起来, 得到的总和为 sum.
3. **sum % 3 得到这个单独数的第 i 位.**
4. 通过位运算将这个第 i 位移到正确位置, 遍历完所有位得到最终结果.


**Code:**
```java
class Solution {    // 找到只出现一次的数. (其他都出现三次.)
public:
    int singleNumber(vector<int>& nums) {
        // 将所有数的 32bit 中的一位加起来, %3 得到只出现一次的 bit.
        int res = 0, sum;
        for(int i = 0; i < 32; i++) {
            sum = 0;
            for(int num : nums) {
                sum += ((num >> i) & 1);
            }
            res |= (sum % 3) << i;
        }
        return res;
    }
};
```

**Solution2:**
一次遍历.

**Code**
```java
class Solution {    // k = 3, p = 1
public:             // mask = "11".
    int singleNumber(vector<int>& nums) {
        int x2 = 0, x1 = 0, mask;
        for(int num : nums) {
            x2 ^= x1 & num;
            x1 ^= num;
            mask = ~(x2 & x1);
            x2 &= mask;
            x1 &= mask;
        }
        return x1;
    }
};
```
**分析:**
**考虑一个数 num 进行运算.(实际上所有数按照不同顺序进行运算, 结果是一样的)**
1. 注意上面这条事项.
2. 考虑 num 出现一次. 则有 **x2 = 0, x1 = num**.
3. num 出现第二次, 则有 **x2 = num, x1 = 0**.
4. num 出现第三次
	4.1 先有 **x2 = num, x1 = num**.
	4.2 **mask 将变成 0, 则 x2 = 0, x1 = 0**

该算法中的各个变量的含义:
1. x1 用于记录 num 出现一次. x2 用于记录 num 出现两次.

2. 当 x2 = num, x1 = num, mask = ~(x2 & x1) 为零.
3. 由于 k = 3, 因此, **当 num 的次数达到三次, 则应将 x2, x1 清零**, 因此, mask 的构造形式根据 k 的值来定. **例如:**
	3.1 k = 2. mask = ~(x2 & ~x1).
	3.2 k = 4. mask = ~(x3 & ~x2 & ~x1). (多加一个用于记录的 x3)

4. 因此, 最终某个数 x 只出现一次时, 将会由 x1 记录.
5. 假如 p = 2. 则(x2 = x, x1 = 0), 则因返回 x2.

**总结:**
**将 x2,x1 视为二进制中位, x2 权重为 2, 记录 num 出现两次. x1 权重为 1, 记录 num 出现一次.**
**根据 k 的值, 构造 mask 的形式, 并根据需要使用足够的变量(Xn, Xn-1, ... x2, x1).**
**这里使用一个数进行分析, 但实际上, 所有数一起运算, 不定的顺序, 结果均是一样的**

**贴几个其他情况的代码:**
```java
// k = 2, p = 1. 无需构造 mask.
public int singleNumber(int[] nums) {
    int x1 = 0;

    for (int i : nums) {
        x1 ^= i;
    }

    return x1;
}
```

```java
// k = 5, p = 3.
// k = 5 => "101" => 构造 mask = ~(x1 & ~x2 & x3).
public int singleNumber(int[] nums) {
    int x1 = 0, x2 = 0, x3  = 0, mask = 0;

    for (int i : nums) {
        x3 ^= x2 & x1 & i;
        x2 ^= x1 & i;
        x1 ^= i;
        mask = ~(x1 & ~x2 & x3);
        x3 &= mask;
        x2 &= mask;
        x1 &= mask;
    }

    return x1;  // Since p = 3, in binary form p = '011', then p1 = p2 = 1, so we can return either x1 or x2. 
    // If p = 4, in binary form p = '100', only p3 = 1, which implies we can only return x3.
    // Or alternatively we can simply return (x1 | x2 | x3).
    }
```

**参考的是 LeetCode 的 Discuss**
##### [Detailed explanation and generalization of the bitwise operation method for single numbers](https://leetcode.com/problems/single-number-ii/discuss/43295/Detailed-explanation-and-generalization-of-the-bitwise-operation-method-for-single-numbers)