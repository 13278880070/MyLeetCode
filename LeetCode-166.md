##### [166. Fraction to Recurring Decimal](https://leetcode.com/problems/fraction-to-recurring-decimal/)

**Description**
```
给定一个分子, 一个分母.
返回计算得到的结果.
如果小数部分有重复的, 则将重复部分用 () 括起来.
```

**Example**
```
Input: numerator = 1, denominator = 2
Output: "0.5"
```

```
Input: numerator = 2, denominator = 1
Output: "2"
```

```
Input: numerator = 2, denominator = 3
Output: "0.(6)"
```

**Solution**
1. 处理正负号(异或)
2. 处理整数部分.
3. 处理小数部分

**Code**
```java
class Solution {	// 计算除法.
public:				// 哈希表!.
    string fractionToDecimal(int num, int denom) {
        if(num == 0) return "0";
        string res;
		// 强制转化为 long 类型
        long numerator = (long)num;
        long denominator = (long)denom;
        // 处理正负.
        if((numerator < 0) ^ (denominator < 0)) res += '-';
        numerator = abs(numerator);
        denominator = abs(denominator);
		// 处理整数部分.
        res += to_string(numerator / denominator);
        if((numerator %= denominator) == 0) return res;

        // 处理小数部分.
        res += '.';
        unordered_map<int, int> map;
        // map 来记录前面出现过的 分子.
        // <num, index>
        while (numerator != 0) {
        	if(map.find(numerator) == map.end()) {
        		map[numerator] = res.length();
        		numerator *= 10;
	        	res += to_string(numerator / denominator);
	        	numerator %= denominator;
        	} else {	// 前面出现过相同的"分子"
        		res.insert(res.begin()+map[numerator], '(');
        		res += ')';
        		break;
        	}
        }
        return res;
    }
};
```

比较难得部分是处理小数部分得代码. 使用`unordered_map`来存储被除数.
```
unordered_map<num, index>
其中 num 表示当前被除数, index 记录被除数第一次出现时对应结果所在 res 的下标.
记录该下标得作用是后期出现一旦出现重复被除数, 即出现了小数中得重复部分.
则应在该重复被除数第一次出现的位置之前插入 "(".
例如:
2 / 3
=> 被除数: 2, 得到余数: 6. 当前 res: "0.6". 且 map[2] = 2.
=> 被除数: 2, map[2] 不为空, 则在 res 下标 map[2] 处插入 "(", 最后再插入")"结束.
```

