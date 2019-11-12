**题目链接** [LeetCode-347](https://leetcode.com/problems/top-k-frequent-elements/)
**题目描述**
给定一个非空的整数数组，返回其中出现频率前 k 高的元素。

示例 1:

输入: nums = [1,1,1,2,2,3], k = 2
输出: [1,2]

#### 思路一
根据自己的做法，使用一个**列为2**的二维数组来存储 ```<元素, 个数>``` 。
根据二维数组的第二列**(元素个数)**, 利用快速排序对二维数组进行排序。
取出二维数组中**前 k 个**的 ```元素```值即可。
AC代码 : 
```java 
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        List<Integer> list = new ArrayList<>();
        Arrays.sort(nums, 0, nums.length);
        int[][] count = new int[nums.length][2];
        int len = 0, l = 0;
        for(int i = 0; i < nums.length-1; i++) {
        	if(nums[i] != nums[i+1]) {
        		count[len][0] = nums[i];
        		count[len][1] = i+1-l;
        		l = i+1;
        		len++;
        	}
        }
        count[len][0] = nums[nums.length-1];
        count[len][1] = nums.length - l;
        len++;

        q_sort(count, 0, len-1);

        for(int i = len-1; i > len-1-k; i--) {
        	list.add(count[i][0]);
        }
        return list;
    }

    public void q_sort(int[][] nums, int l, int r) {
        if(l >= r) return;
        int i = l;
        int j = r;
        while (l < r) {
            while (nums[l][1] <= nums[i][1] && l < j) l++;
            while (nums[r][1] > nums[i][1] && r > i) r--;
            if(l >= r) break;
            swap(nums, l, r);
        }
        swap(nums, i, r);
        q_sort(nums, i, r-1);
        q_sort(nums, l, j);
    }

    public void swap(int[][] nums, int i, int j) {
        int[] temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```

#### 思路二
这里借鉴了网上别人的做法。
遍历数组，使用 ```HashMap``` 来存储 <元素，个数> 键值对。
使用**桶 bucket** 来存储出现频数为 i 的元素。
如在 bucket[i] 中存放出现频数为 i 的**元素的集合**
代码:
```java
class Solution {
    public List<Integer> topKFrequent(int[] nums, int k) {
        Map<Integer, Integer> map = new HashMap<>();
        List<Integer> list = new ArrayList<>();

        for (int item : nums) {
        	map.put(item, map.getOrDefault(item, 0) + 1);
        }

        List<Integer>[] bucket = new List[nums.length+1];
        // bucket[i] 中存放 出现频数为 i 的 元素

        for(int key : map.keySet()) {
        	int value = map.get(key);			/* 对于每个频数 */
            if(bucket[value] == null) {
                bucket[value] = new ArrayList<>();
            }
        	bucket[value].add(key);				/* 将其对应的 元素值 存放进来 */
        }

        for(int i = nums.length; i >= 0 && list.size() < k; i--) {
			/* 从最后一个桶开始遍历，将 k 个元素放到返回的List中 */
            if(bucket[i] != null) list.addAll(bucket[i]);
        }
        return list;
    }
}
```

## 关于桶排序
###### 参考博客： [排序算法之桶排序的深入理解以及性能分析](https://blog.csdn.net/m0_37477061/article/details/82048174)

实现思路与步骤
思路
- 设置固定空桶数
- 将数据放到对应的空桶中
- 将每个不为空的桶进行排序
- 拼接不为空的桶中的数据，得到结果

#### 步骤演示
假设一组数据(20长度)为
> **[63,157,189,51,101,47,141,121,157,156,194,117,98,139,67,133,181,13,28,109] **

现在需要按5个分桶，进行桶排序，实现步骤如下:

- 找到数组中的最大值194和最小值13，然后根据桶数为5，计算出每个桶中的数据范围为(194-13+1)/5=36.4

- 遍历原始数据，(以第一个数据63为例)先找到该数据对应的桶序列**Math.floor(63 - 13) / 36.4) =1**，然后将该数据放入序列为1的桶中(从0开始算)
- 当向同一个序列的桶中第二次插入数据时，判断桶中已存在的数字与新插入的数字的大小，**按从左到右，从小打大**的顺序插入。如第一个桶已经有了63，再插入51，67后，桶中的排序为(51,63,67) 一般通过链表来存放桶中数据，但js中可以使用数组来模拟
- 全部数据装桶完毕后，按序列，**从小到大合并**所有非空的桶(如0,1,2,3,4桶)
- 合并完之后就是已经排完序的数据

![pic1](https://raw.githubusercontent.com/wiki/13278880070/PIC/5-29-pic1.png)

**Java 示例代码**
```java
public static double[] bucketSort(double arr[], int bucketCount) {
 
    int len = arr.length;
    double[] result = new double[len];
    double min = arr[0];
    double max = arr[0];
    //找到最大值和最小值
    for (int i = 1; i < len; i++) {
        min = min <= arr[i] ? min: arr[i];
        max = max >= arr[i] ? max: arr[i];
    }
    //求出每一个桶的数值范围
    double space = (max - min + 1) / bucketCount;
    //先创建好每一个桶的空间,这里使用了泛型数组
    ArrayList < Double > [] arrList = new ArrayList[bucketCount];
    //把arr中的数均匀的的分布到[0,1)上，每个桶是一个list，存放落在此桶上的元素   
    for (int i = 0; i < len; i++) {
        int index = (int) Math.floor((arr[i] - min) / space);
        if (arrList[index] == null) {
            //如果链表里没有东西
            arrList[index] = new ArrayList < Double > ();
            arrList[index].add(arr[i]);
        } else {
            //排序
            int k = arrList[index].size() - 1;
            while (k >= 0 && (Double) arrList[index].get(k) > arr[i]) {
                if (k + 1 > arrList[index].size() - 1) {
                    arrList[index].add(arrList[index].get(k));
                } else {
                    arrList[index].set(k + 1, arrList[index].get(k));
                }
                k--;
            }
            if (k + 1 > arrList[index].size() - 1) {
                arrList[index].add(arr[i]);
            } else {
                arrList[index].set(k + 1, arr[i]);
            }
        }
 
    }
 
    //把各个桶的排序结果合并  ,count是当前的数组下标
    int count = 0;
 
    for (int i = 0; i < bucketCount; i++) {
        if (null != arrList[i] && arrList[i].size() > 0) {
            Iterator < Double > iter = arrList[i].iterator();
            while (iter.hasNext()) {
                Double d = (Double) iter.next();
                result[count] = d;
                count++;
            }
        }
    }
    return result;
}
//开始排序,其中arr为需要排序的数组
double[] result = bucketSort(arr,bucketCount);
```
