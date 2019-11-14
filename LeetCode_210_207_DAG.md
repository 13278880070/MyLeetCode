##### [210. Course Schedule II](https://leetcode.com/problems/course-schedule-ii/)
##### [207. Course Schedule](https://leetcode.com/problems/course-schedule/)

**Description**
There are a total of n courses you have to take, labeled from 0 to n-1.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: [0,1]

Given the total number of courses and a list of prerequisite pairs, return the ordering of courses you should take to finish all courses.

大概意思
```
共有 n 门课程, 分别记为 0 ~ n-1
课程之间有先后顺序之分, 比如学习课程 1 之前, 你必须先学习课程 0, 用一对整数来表示就是 [1, 0]

同理, [3, 1] 表示学习课程 3 之前, 必须先学习课程 1

LeetCode 207 要求判断是否存在这样的序列, 使得可以完成所有的课程.
LeetCode 210 要求返回一个可以完成的学习课程序列.
```

**Example1**
```
Input: 2, [[1,0]] 
Output: [0,1]
Explanation: There are a total of 2 courses to take. To take course 1 you should have finished   
             course 0. So the correct course order is [0,1] .
```

**Example2**
```
Input: 4, [[1,0],[2,0],[3,1],[3,2]]
Output: [0,1,2,3] or [0,2,1,3]
Explanation: There are a total of 4 courses to take. To take course 3 you should have finished both courses 1 and 2.
Both courses 1 and 2 should be taken after you finished course 0
             So one correct course order is [0,1,2,3]. Another correct ordering is [0,2,1,3] .
```

**Solution**
两个题目基本上就是同一问题, 这里要解决的问题是就是**有向无环图的拓扑排序**.

**[拓扑排序](https://zh.wikipedia.org/wiki/%E6%8B%93%E6%92%B2%E6%8E%92%E5%BA%8F)**: 由一个有向无环图的顶点组成的序列, 当且仅当满足以下条件的序列时, 称为该图的一个拓扑排序:

1. 每个顶点出现且仅出现一次;

2. 若 A 在序列中排在 B 的前面, 则图中不存在从 B 到 A 的路径;

**拓扑排序算法**:

1. 找到所有入度为零的顶点, 添加到队列

2. 从队列移出一个顶点 x, 打印该顶点的值 (需要的话), 在图中删除与顶点 x 相连的边.

3. 将新产生的入度为零的顶点加入队列, 重复 2, 3 直到队列为空

<br>
##### Code1: 有向无环图拓扑排序的实现

有向无环图的两种实现方案:
1. 邻接矩阵法
2. 邻接表法

**邻接矩阵法**
```java
// 邻接矩阵实现
class Graph {
private:
	int vnum;			 // 顶点个数
	queue<int> gqueue;	// 辅助队列
	int **graph;		  // 二维数组
	int *indegree;		// 入度记录数组

	int count;			// 记录未处理顶点数

public:
	Graph(int v) {	// 构造函数, 初始化数组
		this->vnum = v;
		graph = new int*[v];
		indegree = new int[v];

		for(int i = 0; i < v; i++) {
			graph[i] = new int[v];
			indegree[i] = 0;

			for(int j = 0; j < v; j++) graph[i][j] = 0;
		}

		this->count = v;
	}

	~Graph() {
		delete []indegree;
		for(int i = 0; i < vnum; i++) delete []graph[i];
		delete []graph;
	}

	// 添加 a->b 的边
	void addEdge(int a, int b) {
		graph[a][b] = 1;
		indegree[b]++;
	}

	// 得到拓扑排序结果, 如果没有正确的结果, 返回 false
    // vec 实参, 存储拓扑排序序列
	bool getSort(vector<int>& vec) {
		int x;

		// 首先将所有入度为 0 的结点入队列
		for(int i = 0; i < vnum; i++) {
			if(!indegree[i]) gqueue.push(i);
		}

		while (!gqueue.empty()) {
			x = gqueue.front();
			gqueue.pop();

			vec.push_back(x);
			count--;

			for(int i = 0; i < vnum; i++) {
				if(graph[x][i]) {
                	// 删除与 x 相连的边
                    // 将新的入度为零的顶点加入队列
					indegree[i]--;
					if(!indegree[i]) gqueue.push(i);
				}
			}
		}
		// count 不为零, 而队列为空, 说明有环
		return !count;
	}

};
```

**邻接表法**
```java
// 邻接表实现有向无环图. DAG
class Graph {
private:
	int v;				// 顶点个数
	list<int> *adj;		// 邻接表项
	int *indegree;		// 每个顶点入度
	int count;			// 图中剩余顶点数

	queue<int> graphq;		// 存储入度为 0 的顶点

public:
	Graph(int v) {
		this->v = v;
		this->count = v;
		adj = new list<int>[v];
		indegree = new int[v];

		for(int i = 0; i < v; i++) {
			indegree[i] = 0;
		}
	}

	// 添加 a -> b 的边
	void addEdge(int a, int b) {
		adj[a].push_back(b);
		indegree[b]++;
	}

	// 拓扑排序
	bool getSort(vector<int> &vec) {
		for(int i = 0; i < v; i++) {
			if(!indegree[i]) graphq.push(i);
		}

		while (!graphq.empty()) {
			int x = graphq.front();
			graphq.pop();
			vec.push_back(x);
			count--;

			for(int ele : adj[x]) {
				indegree[ele]--;
				if(!indegree[ele]) graphq.push(ele);
			}
		}

		return !count;
	}
};
```

##### Code2: LeetCode 解答
**LeetCode. 207**
```java
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
    	Graph graph = Graph(numCourses);

    	for(vector<int> vec : prerequisites) {
    		graph.addEdge(vec[1], vec[0]);
    	}
        
    	vector<int> vec;
    	return graph.getSort(vec);
    }
};
```

**LeetCode. 210**
```java
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        Graph graph = Graph(numCourses);
        for(vector<int> vec : prerequisites) {
        	graph.addEdge(vec[1], vec[0]);
        }

        vector<int> retvec;
        bool re = graph.getSort(retvec);

        return re ? retvec : vector<int>{};
    }
};
```

邻接表比邻接矩阵不止快一点...