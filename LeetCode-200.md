##### [200. Number of Islands](https://leetcode.com/problems/number-of-islands/)

**Description**
Given a 2d grid map of `'1'`s (land) and `'0'`s (water), count the
number of islands. An island is surrounded by water and is formed
by connecting adjacent lands horizontally or vertically. You may
assume all four edges of the grid are all surrounded by water.

**Example1**
```
Input:
11110
11010
11000
00000

Output: 1
```

**Example2**
```
Input:
11000
11000
00100
00011

Output: 3
```

**Solution**
由于之前做过一个使用**并查集**的类似的题目.
###### [链接: LeetCode-130 与 并查集](https://blog.csdn.net/hudaJY/article/details/102842671)
因此, 直接写代码.

**Code**
```java
class UF
{
private:
	int *pa;
	int *rank;
	int count;
public:
	UF(int N) {
		count = N;
		pa = new int[N];
		rank = new int[N];

		for(int i = 0; i < N; i++) {
			pa[i] = i;
			rank[i] = 0;
		}
	}

	~UF() {
		delete []pa;
		delete []rank;
	}

	// 是否同一颗子树.
	bool find(int a, int b) {
		return getroot(a) == getroot(b);
	}

	// 返回当前子树根节点.
	int getroot(int a) {
		while (pa[a] != a) a = pa[a];
		return a;
	}

	// 将任意两个结点所在的树合并.
	void unichild(int a, int b) {
		a = getroot(a);
		b = getroot(b);
		if(a != b) {
			if(rank[a] > rank[b]) {
				pa[b] = a;
			} else if(rank[a] < rank[b]) {
				pa[a] = b;
			} else {
				pa[b] = a;
				rank[a]++;
			}
			count--;
		}
	}
    
    int getcount() {return count;}
};

class Solution {	// '1' 代表 land, '0' 代表 water
public:				// 返回所有的 "islands"   (个人思路, 想到了之前的并查集!)
    int numIslands(vector<vector<char>>& grid) {
    	int n = grid.size();
    	if(!n) return 0;
    	int m = grid[0].size();


        UF uf = UF(m*n+1);	// 使用一个虚拟的 root, 来合并所有的'1'
        for(int i = 0; i < n; i++) {
        	for(int j = 0; j < m; j++) {
        		if(grid[i][j] == '0') {
        			uf.unichild(i*m+j, n*m);
        		} else {
        			if(i != 0 && grid[i-1][j] == '1') uf.unichild((i-1)*m+j, i*m+j);
        			if(j != 0 && grid[i][j-1] == '1') uf.unichild(i*m+j-1, i*m+j);
        		}
        	}
        }

        for(int i = 0; i < n; i++) {
        	for(int j = 0; j < m; j++) {
                cout<<uf.getroot(i*m + j)<<" ";
            }
            cout<<endl;
        }

        return uf.getcount()-1;
    }
};
```
