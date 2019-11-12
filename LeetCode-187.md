##### [LeetCode-187.Repeated DNA Sequences](https://leetcode.com/problems/repeated-dna-sequences/)

**Description**
```
All DNA is composed of a series of nucleotides abbreviated as A, C, G, and T, for example: "ACGAATTCCG". When studying DNA, it is sometimes useful to identify repeated sequences within the DNA.

Write a function to find all the 10-letter-long sequences (substrings) that occur more than once in a DNA molecule.

大意: 找出给定字符串中出现过多次(大于一次)的长度为 10 的子字符串.
```

**Example**
```
Input: s = "AAAAACCCCCAAAAACCCCCCAAAAAGGGTTT"

Output: ["AAAAACCCCC", "CCCCCAAAAA"]
```

**Solution**
map + 位运算

该题的主要特征是只有 4 个字符.
(第一次做, 直接 find_first_of, 毫不意外, 超时了. 看了下 topic, 位运算, 想了下, 嗯, 不会做)
使用一个整数来代表每个字符. `0: 'A', 1: 'C', 2: 'G', 3: 'T'`
长度为 10 的子串. 我们使用位运算将这个子串对应的 10 个整数 (范围0~3) **存到一个整数中去**.
一个整数 32 bit. 注意到`0 => 00`, `1 => 01`, `2 => 10`, `3 => 11`.
因此, 这里使用每两个 bit 来存储一个数(范围0~3).
使用一个整数 sum(32 bit) 即可将所有的 10 个数存储起来(10 个数对应 20 bit).
使用 map 记录 sum 出现次数, 当 sum 出现第二次时, 则将当前位置的前 10 个字符添加到最终结果中.

**Code**
```java
class Solution {
public:
	// 使用位来存储每个字符 'A', 'C', 'G', 'T'
    // 最终一个字符对应 2 个 bit
    vector<string> findRepeatedDnaSequences(string s) {
        int len = s.length();
        vector<string> retvec;
        if(len <= 9) return retvec;

        // 00, 01, 10, 11. 每个字符使用两位存储
        unordered_map<char, int> map = {{'A', 0}, {'C', 1}, {'G', 2}, {'T', 3}};
        unordered_map<int, int> countmap;	// 记录每个 sum 出现次数.

       	int i = 0, sum = 0;
       	while (i < 9) sum = (sum << 2) | map[s[i++]];

       	while (i < len) {
       		sum &= 0x3FFFF;		// 只存储 9 个 0~3, 清空其他位.
       		sum = (sum << 2) | map[s[i++]];	// 新添加一个 0~3, 得到 sum.
       		if(countmap[sum]++ == 1) retvec.push_back(s.substr(i-10, 10));
               // 当前的 10 个 0~3 在 countmap 中已有, 则将当前字符往前的 10 个字串添加到 retvec 中.
       	}
       	return retvec;
    }
};
```

