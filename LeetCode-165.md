##### [165. Compare Version Numbers](https://leetcode.com/problems/compare-version-numbers/)
**Description**
```
比较两个字符串类型表示的版本 version1, version2.
如果 version1 > version2 return 1;
如果 version1 < version2 return -1;
如果 version1 == version2 return 0;
```

**Example**
```
Input: version1 = "0.1", version2 = "1.1"
Output: -1
```

```
Input: version1 = "7.5.2.4", version2 = "7.5.3"
Output: -1
```

```
Input: version1 = "1.01", version2 = "1.001"
Output: 0
Explanation: Ignoring leading zeroes, both “01” and “001" represent the same number “1”
```

**Solution**
逐个字符进行处理, index1, index2 记录当前处理字符的下标.
外层循环条件为: version1 或 version2 还没有处理完成时.
内层循环:
处理每个用 '.' 隔开的单元.
如果当前字符串已经处理完毕, 则`index_i >= len_i`, 得到的`sum_i`即为 0.


**Code**
```java
class Solution {	// 版本比较
public:				// 单个字符进行处理.
    int compareVersion(string version1, string version2) {
        int len1 = version1.length();
        int len2 = version2.length();
        int sum1, sum2, index1 = 0, index2 = 0;
        while(index1 < len1 || index2 < len2) {
        	sum1 = 0;
        	sum2 = 0;

        	while (index1 < len1 && version1[index1] != '.') {
        		sum1 = sum1*10 + version1[index1++] - '0';
        	}

        	while (index2 < len2 && version2[index2] != '.') {
        		sum2 = sum2*10 + version2[index2++] - '0';
        	}

        	if(sum1 > sum2) return 1;
        	if(sum1 < sum2) return -1;

            index1++;
            index2++;
            // 跳过 '.'
            // 如果 index_i >= len_i 也没有影响.
        }
        return 0;
    }
};
```