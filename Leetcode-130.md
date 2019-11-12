##### LeetCode-130

**Solution1:**
1. 遍历边界点, 如果该值为 'O', 则在该处进行递归继续扩展. 并将走过的路程上的点赋值为 '*'.
2. 遍历整个二维空间, 将所有为 '*' 的赋值为 'O', 其他均为 'X'.

**Code:**
```java
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        int n = board.size();
        if(n == 0) return ;
        int m = board[0].size();
        if(n <= 2 || m <= 2) return ;

        for(int j = 0; j < m; j++) {
        	if(board[0][j] == 'O') {
        		board[0][j] = '*';
        		solveit(0, j, board);
        	}
        	if(board[n-1][j] == 'O') {
        		board[n-1][j] = '*';
        		solveit(n-1, j, board);
        	}
        }

        for(int i = 1; i < n-1; i++) {
        	if(board[i][0] == 'O') {
        		board[i][0] = '*';
        		solveit(i, 0, board);
        	}
        	if(board[i][m-1] == 'O') {
        		board[i][m-1] = '*';
        		solveit(i, m-1, board);
        	}
        }
        
        for(int i = 0; i < n; i++) {
        	for(int j = 0; j < m; j++) {
                if(board[i][j] == '*') board[i][j] = 'O';
                else board[i][j] = 'X';
            }
        }
    }

    void solveit(int i, int j, vector<vector<char>>& board) {
    	if(i > 0 && board[i-1][j] == 'O') {
    		board[i-1][j] = '*';
    		solveit(i-1, j, board);
    	}
    	if(i < board.size()-1 && board[i+1][j] == 'O') {
    		board[i+1][j] = '*';
    		solveit(i+1, j, board);
    	}
    	if(j > 0 && board[i][j-1] == 'O') {
    		board[i][j-1] = '*';
    		solveit(i, j-1, board);
    	}
    	if(j < board[0].size()-1 && board[i][j+1] == 'O') {
    		board[i][j+1] = '*';
    		solveit(i, j+1, board);
    	}
    }
};
```

**Solution2:**
1. 使用并查集, 顺便了解了并查集的概念及其实现.
2. 将边界为 'O' 的点, 均"连接"到一个额外的虚拟点. 对于每个为 'O' 的内部点, 判断其周围是否为 'O', 若是, 则将这两个结点所在的子树"连接"到一起.
3. "连接"即指并查集中的 union 操作. 归并两个子树.

**Code1:**
并查集的实现:
```java
class UF {
private:
	int *parent;	// 父指针数组.
	int *rank;		// rank. (这里就是每个子树的高度.)
	int count;		// 子树数量.

	int find(int p) {		// 返回该节点所在树的根节点.
		while (p != parent[p]) {
			p = parent[p];
		}
		return p;
	}

public:
	UF(int N) {
		parent = new int[N];
		rank = new int[N];

		// 初始每个结点都是一个子树.
		for(int i = 0; i < N; i++) {
			parent[i] = i;
			rank[i] = 0;
		}

		count = N;
	}
	~UF() {
		delete [] parent;
		delete [] rank;
	}

	void uni(int p, int q) {	// 合并两个子树(根节点).
		if(rank[p] > rank[q]) {
			parent[q] = p;
		} else if(rank[p] < rank[q]) {
			parent[p] = q;
		} else {
			parent[p] = q;
			rank[q]++;
		}
		count--;
	}

	void unichild(int p, int q) {	// 合并任意两个结点所在的树.
		if(diff(p, q)) {
			uni(find(p), find(q));
		}
	}

	bool diff(int p, int q) {	// 是否在同一颗树中.
		int Pp = find(p);
		int Pq = find(q);
		if(Pp == Pq) return false;
		return true;
	}

	int getcount() {return count;}
};
```

应用到 LeetCode 中:
```java
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        int n = board.size();
        if(n == 0) return ;
        int m = board[0].size();

        UF *uf = new UF(n*m + 1);	// 最后一个结点为虚拟结点, 用于连接所有 边界'O' 以及与 边界'O' 连通的结点.

        for(int i = 0; i < n; i++) {
        	for(int j = 0; j < m; j++) {
        		if(board[i][j] == 'O') {
        			if(i == 0 || j == 0 || i == n-1 || j == m-1) {	// 边界 'O'
        				uf->unichild(i*m + j, m*n);
        			} else {	// 非边界 'O'
        				if(board[i-1][j] == 'O') {
        					uf->unichild((i-1)*m + j, i*m + j);
        				}
        				if(board[i+1][j] == 'O') {
        					uf->unichild((i+1)*m + j, i*m + j);
        				}
        				if(board[i][j-1] == 'O') {
        					uf->unichild(i*m + j-1, i*m + j);
        				}
        				if(board[i][j+1] == 'O') {
        					uf->unichild(i*m + j+1, i*m + j);
        				}
        			}
        		}
        	}
        }

        for(int i = 0; i < n; i++) {
        	for(int j = 0; j < m; j++) {
        		if(board[i][j] == 'O' && uf->diff(i*m + j, m*n)) {
        			board[i][j] = 'X';
        		}
        	}
        }
    }
};
```

**关于并查集**
成员变量:
1. int *parent: 指向每个结点的父结点. __初始化的并查集每个结点都是一个子树__, 每个结点的 parent 均为其本身.
2. int *rank: 每个结点优先级. 用于归并两个子树时, 指定哪个作为父结点, 哪个作为子节点. 上面代码的实现中, 以树的深度作为优先级.
3. int count: 整个并查集中的树的数量. 初始每个结点均为子树, count = N;