# BFS

常用于搜索最短路径/最小操作次数(无权图或者权值相等的图)

通过队列实现，对于每个节点，横向搜索可能的下一个节点(重复的忽略，用hashmap去重)，每次都走一步，因此最先到达终点的 "路径" 最短



## 相关题目

* [403. 青蛙过河](https://leetcode-cn.com/problems/frog-jump/)
* [2059. 转化数字的最小运算数](https://leetcode-cn.com/problems/minimum-operations-to-convert-number/)

* [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)
* [1345. 跳跃游戏 IV](https://leetcode-cn.com/problems/jump-game-iv/)
* [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)
* ...



## 模板

```java
public class Solution {
    public void BFS() {
        //1.初始化queue和map，对起点
        while(!q.isEmpty()) {
            //2.取队列首节点，并获取路径长度
            int cur = q.pollFirst();
            int step = map.get(cur);
            if (cur == end) return step;
            
            //3.根据题意，遍历搜索空间
            //若已经搜索到过，忽略
            //否则加入map 和 queue
        }
        
        return -1;
    }
}
```



### [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)

```c++
class Solution {
public:
    int integerReplacement(long n) {
        //map保存节点的值以及 路径长度/操作次数
        unordered_map<long, long> map;
        //队列保存每一个搜索到的节点
        queue<long> q;
        //对map、queue进行初始化，路径长度/操作次数 = 0，并且入队列
        map[n] = 0;
        q.push(n);
        
        while (!q.empty()) {
            //头节点出队列，并通过map的映射得到 路径长度
            long cur = q.front();
            q.pop();
            int step = map[cur];
            
            //判断是否到达终点
            if (cur == 1) return step;
            
            //根据题意，搜索节点的下一个状态，已经搜索到的则忽略
            //否则map[next] = step + 1;q.push(next);
            if (cur % 2 == 0 && !map.count(cur / 2)) {
                map[cur / 2] = step + 1;
                q.push(cur / 2);
            }
            if (cur % 2 == 1 && !map.count(cur + 1)) {
                map[cur + 1] = step + 1;
                q.push(cur + 1);
            }
            if (cur % 2 == 1 && !map.count(cur - 1)) {
                map[cur - 1] = step + 1;
                q.push(cur - 1);
            }
            
        }
        return -1;
    }
};
```



### [2059. 转化数字的最小运算数](https://leetcode-cn.com/problems/minimum-operations-to-convert-number/)

```java
class Solution {
    public int minimumOperations(int[] nums, int start, int goal) {
        Map<Integer, Integer> map = new HashMap<>();
        Deque<Integer> q = new ArrayDeque<>();
        map.put(start, 0);
        q.offer(start);

        while (!q.isEmpty()) {
            int cur = q.poll();
            int step = map.get(cur);
            if (cur == goal) return step;
            if (cur < 0 || cur > 1000) continue;
            for (int num : nums) {
                int[] next = new int[]{cur + num, cur - num, cur ^ num};
                for (int n : next) {
                    if (!map.containsKey(n)) {
                        map.put(n, step + 1);
                        q.offer(n);
                    }
                }
            }
        }
        return -1;
    }
}
```

