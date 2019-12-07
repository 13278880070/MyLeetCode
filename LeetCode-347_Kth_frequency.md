
**问题描述**
&emsp;&emsp;给定一组整数, 返回出现频率最高的 k 个数。

**Solution1**
思路: map + priority_queue.

&emsp;&emsp;使用 `unordered_map` + `priority_queue` 来实现。 `map` 记录每个数出现的频率, 最后使用最小堆来存放每个 `map` 对应的`pair<int, int>`, 优先级为 `pair.second`。
&emsp;&emsp;在添加 `pair<int, int>` 对到 `queue` 中时, 每当 `queue` 的大小 > k, 则 `pop` 出当前最小堆中的一个元素。

**Code**
```java
struct Compare
{
	bool operator()(const pair<int, int> &a, const pair<int, int> &b) {
		return a.second > b.second;	// 这里应该使用最小堆, 将优先级小的 pop 掉, 所以使用 > 号.
	}
};

class Solution {	// 返回出现频率前 k 个数
public:

    vector<int> topKFrequent(vector<int>& nums, int k) {
        unordered_map<int, int> map;
        for (int ele : nums) {
        	map[ele]++;
        }

        priority_queue<pair<int, int>, vector<pair<int, int>>, Compare> q;

        unordered_map<int, int>::iterator it = map.begin();

        for (; it != map.end(); it++) {
        	q.push(pair<int, int>(it->first, it->second));
        	if (q.size() > k) q.pop();
        }

        vector<int> retvec;
        while (!q.empty()) {
        	retvec.push_back(q.top().first);
        	q.pop();
        }

        return retvec;
    }
};
```

**Solution2**
思路: 使用 `map` + `桶`

&emsp;&emsp;`map` 的作用依旧是存储每个元素对应的出现频率。 其中`Bucket[i]`存放的是**出现频率为 i+1 的元素**。
&emsp;&emsp;最后从`Bucket[len-1]`开始遍历所有 `Bucket`, 将 `Bucket[i]` 的每个元素添加到 `retvec(返回值)` 中, 当 `retvec` 中元素个数达到 k 时退出。

**Code**
```java
class Solution {	// 桶排序, Bucket[i] 存放出现 频率 为 i 的数.
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
    	int len = nums.size();
        vector<vector<int> > Bucket(len, vector<int>());

        unordered_map<int, int> map;
        for (int ele : nums) map[ele]++;

        unordered_map<int, int>::iterator it;
    	for (it = map.begin(); it != map.end(); it++) {
    		Bucket[it->second-1].push_back(it->first);
    	}

    	vector<int> retvec;
    	for (int i = len-1; i >= 0; i--) {
    		for (int ele : Bucket[i]) {
    			retvec.push_back(ele);
    		}
    		if (retvec.size() >= k) break;
    	}
        
        return retvec;
    }
};
```