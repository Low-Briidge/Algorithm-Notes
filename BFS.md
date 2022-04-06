# BFS

常用于搜索最短路径/最小操作次数(无权图或者权值相等的图)

通过队列实现，对于每个节点，横向搜索可能的下一个节点(重复的忽略，用hashmap去重)，每次都走一步，因此最先到达终点的 "路径" 最短



## 相关题目

* [403. 青蛙过河](https://leetcode-cn.com/problems/frog-jump/) **
* [2059. 转化数字的最小运算数](https://leetcode-cn.com/problems/minimum-operations-to-convert-number/) **

* [397. 整数替换](https://leetcode-cn.com/problems/integer-replacement/)
* [1345. 跳跃游戏 IV](https://leetcode-cn.com/problems/jump-game-iv/)
* [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/) *
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



#### 双向BFS

```java
class Solution {
public:
    int integerReplacement(long n) {
        if (n == 1) return 0;
        unordered_map<long, long> m1, m2;
        queue<long> q1, q2;
        m1[n] = 0;
        m2[1] = 0;
        q1.push(n);
        q2.push(1);
        while (!q1.empty() && !q2.empty()) {
            if (q1.size() <= q2.size()) {
                int size = q1.size();
                while (size-- > 0) {
                    long cur = q1.front();q1.pop();
                    long step = m1[cur];
                    if (m2.count(cur)) return step + m2[cur];
                    if (cur % 2 == 0 && !m1.count(cur / 2)) {
                        m1[cur / 2] = step + 1;
                        q1.push(cur / 2);
                    }
                    if (cur % 2 == 1 && !m1.count(cur + 1)) {
                        m1[cur + 1] = step + 1;
                        q1.push(cur + 1);
                    }
                    if (cur % 2 == 1 && !m1.count(cur - 1)) {
                        m1[cur - 1] = step + 1;
                        q1.push(cur - 1);
                    }
                }
            }
            else {
                int size = q2.size();
                while (size-- > 0) {
                    long cur = q2.front();q2.pop();
                    long step = m2[cur];
                    if (m1.count(cur)) return step + m1[cur];
                    if (!m2.count(cur * 2)) {
                        m2[cur * 2] = step + 1;
                        q2.push(cur * 2);
                    }
                    if (cur % 2 == 0 && !m2.count(cur + 1)) {
                        m2[cur + 1] = step + 1;
                        q2.push(cur + 1);
                    }
                    if (cur % 2 == 0 && !m2.count(cur - 1)) {
                        m2[cur - 1] = step + 1;
                        q2.push(cur - 1);
                    }
                }
            }
        }
        return 0;
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



### [1345. 跳跃游戏 IV](https://leetcode-cn.com/problems/jump-game-iv/)

* 稠密图的BFS优化 :hammer_and_wrench:

```java
class Solution {
    public int minJumps(int[] nums) {
        int n = nums.length, res = n - 1;
        Map<Integer, List<Integer>> same = new HashMap<>();
        Map<Integer, Integer> map = new HashMap<>();
        Deque<Integer> q = new ArrayDeque<>();
        map.put(0, 0);
        q.offerLast(0);
        for (int i = n - 1; i >= 0; i--) { 
            List<Integer> list = same.getOrDefault(nums[i], new ArrayList<>());
            list.add(i);
            same.put(nums[i], list);
        }
        while (!q.isEmpty()) {
            int cur = q.removeFirst();
            int step = map.get(cur);
            List<Integer> list = same.getOrDefault(nums[cur], new ArrayList<>());

            if (cur == n - 1) res = res < step ? res : step;

            if (cur + 1 < n && !map.containsKey(cur + 1)) {
                map.put(cur + 1, step + 1);
                q.offerLast(cur + 1);
            }
            if (cur - 1 >= 0 && !map.containsKey(cur - 1)) {
                map.put(cur - 1, step + 1);
                q.offerLast(cur - 1);
            }
            for (int index : list) {
                if (!map.containsKey(index)) {
                    map.put(index, step + 1);
                    q.offerLast(index);
                }
            }
            same.remove(nums[cur]);
        }
        return res;
    }
}
```



### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

* 双端BFS  :heavy_check_mark:
* 查询优化 :hammer_and_wrench:



#### 1. 错误解法(左右单个交替搜索可能找到一个次优解就立刻返回，详见收藏夹) :x:

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> set = new HashSet<>(wordList);
        if (!set.contains(endWord)) return 0;
        Map<String, Integer> m1 = new HashMap<>(), m2 = new HashMap<>();
        Deque<String> q1 = new ArrayDeque<>(), q2 = new ArrayDeque<>();
        m1.put(beginWord, 1);
        m2.put(endWord, 1);
        q1.offer(beginWord);
        q2.offer(endWord);

        while (!q1.isEmpty() || !q2.isEmpty()) {
            if (q1.size() >= q2.size()) {
                Map<String, Integer> map = m1;
                Deque<String> q = q1;
                String cur = q.poll();
                int step = map.get(cur);
                // if (cur.equals(endWord)) return step;
                int res = change(cur, set, map, q, m2);
                    if (res > 0) return res;
            }
            else {
                Map<String, Integer> map = m2;
                Deque<String> q = q2;
                String cur = q.poll();
                int step = map.get(cur);
                // if (cur.equals(endWord)) return step;
                int res = change(cur, set, map, q, m1);
                if (res > 0) return res;
            }
            
        }
        return 0;
    }
    public int change(String s, Set<String> set, Map<String, Integer> map, Deque<String> q, Map<String, Integer> m) {
        char[] a = s.toCharArray();
        for (int i = 0; i < a.length; i++) {
            char origin = a[i];
            for (char c = 'a'; c <= 'z'; c++) {
                if (c == origin) continue;
                a[i] = c;
                String str = String.valueOf(a);
                if (set.contains(str) && !map.containsKey(str)) {
                    map.put(str, map.get(s) + 1);
                    q.offer(str);
                    if (m.containsKey(str)) return map.get(str) + m.get(str) - 1;
                }
            }
            a[i] = origin;
        }
        return -1;
    }
}
```



#### 2. 正确解法 (左右‘逐层’交替搜索):heavy_check_mark:

```java
class Solution {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> set = new HashSet<>(wordList);
        if (!set.contains(endWord)) return 0;
        Map<String, Integer> m1 = new HashMap<>(), m2 = new HashMap<>();
        Deque<String> q1 = new ArrayDeque<>(), q2 = new ArrayDeque<>();
        m1.put(beginWord, 1);
        m2.put(endWord, 1);
        q1.offer(beginWord);
        q2.offer(endWord);

        while (!q1.isEmpty() && !q2.isEmpty()) {
            if (q1.size() <= q2.size()) {
                int res = change(set, m1, q1, m2);
                if (res > 0) return res;
            }
            else {
                int res = change(set, m2, q2, m1);
                if (res > 0) return res;
            }
            
        }
        return 0;
    }
    public int change(Set<String> set, Map<String, Integer> map, Deque<String> q, Map<String, Integer> m) {
        int size = q.size();
        while (size-- > 0) {
            String s = q.poll();
            char[] a = s.toCharArray();
            for (int i = 0; i < a.length; i++) {
                char origin = a[i];
                for (char c = 'a'; c <= 'z'; c++) {
                    if (c == origin) continue;
                    a[i] = c;
                    String str = String.valueOf(a);
                    if (m.containsKey(str)) return map.get(s) + m.get(str);
                    if (set.contains(str) && !map.containsKey(str)) {
                        map.put(str, map.get(s) + 1);
                        q.offer(str);
                    }
                }
                a[i] = origin;
            }
        }
        return -1;
    }
}
```

