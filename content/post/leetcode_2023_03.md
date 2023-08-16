---
title: "力扣做题，2022年3月"
description: "记录一下去年3月份做的力扣题以及自己的一些思路和看题解的思路"
date: "2023-08-17T04:53:39+08:00"
publishDate: "2023-08-17T04:53:39+08:00"
---

### 172.阶乘后的零（中等）3.25

- **题设** ：
  给定一个整数 `n` ，返回 `n!` 结果中尾随零的数量。
  提示 `n! = n * (n - 1) * (n - 2) * ... * 3 * 2 * 1`
  **示例一：**

  ```
  输入：n = 3
  输出：0
  解释：3! = 6 ，不含尾随 0
  ```

  **示例二：**

  ```
  输入：n = 5
  输出：1
  解释：5! = 120 ，有一个尾随 0
  ```

  **示例三：**

  ```
  输入：n = 0
  输出：0
  ```

  **提示：**

     - `0 <= n <= 10^4`

- **解题思路**：
  **方法一：数学**
  n! 尾零的数量即为 n! 中因子 10 的个数，而 10=2×5，因此转换成求 n! 中质因子 2 的个数和质因子 5 的个数的较小值。

  由于质因子 2 的个数不会低于质因子 5 的个数（具体证明见方法二），我们可以仅考虑质因子 5 的个数。

  而 n! 中质因子 5 的个数等于 [1,n] 的每个数的质因子 5 的个数之和，我们可以通过遍历 [1,n] 的所有 55 的倍数求出。

  **方法二：优化计算**
  换一个角度考虑 [1,n] 中质因子 p 的个数。
  [1,n]中 p 的倍数有
  $$
  n_1 = \lfloor \frac{n}{p} \rfloor
  $$
  个，这些数至少贡献出了 n1 个质因子 p。p²的倍数有
  $$
  n_2 = \lfloor \frac{n}{p^2} \rfloor
  $$
  个，由于这些数均为 p 的倍数，因此这些数额外贡献了 n2 个质因子 p。
  依此类推，[1,n] 中质因子 p 的个数为
  $$
  \sum_{k=1}^{\infty}{\lfloor \frac{n}{p^k} \rfloor}
  $$
  上式表明：

        1. [1,n] 中质因子 5 的个数不会低于质因子 2 的个数；
        2. [1,n] 中质因子 5 的个数为

  $$
  \sum_{k=1}^{\infty}{\lfloor \frac{n}{5^k} \rfloor} < \sum_{k=1}^{\infty}{\frac{n}{5^k}} = \frac{n}{4} = O(n)
  $$

  代码实现时，由于
  $$
  \lfloor \frac{n}{5^k} \rfloor = \lfloor \frac{\lfloor \frac{n}{5^{k-1}} \rfloor}{5} \rfloor
  $$

- **参考代码**：
  **方法一：**

  ```java
  class Solution {
      public int trailingZeroes(int n) {
          int ans = 0;
          for (int i = 5; i <= n; i += 5) {
              for (int x = i; x % 5 == 0; x /= 5) {
                  ++ans;
              }
          }
          return ans;
      }
  }
  ```

  **方法二：**

  ```java
  class Solution {
      public int trailingZeroes(int n) {
          int ans = 0;
          while (n != 0) {
              n /= 5;
              ans += n;
          }
          return ans;
      }
  }
  ```

- **总结**：寄



### 1606.找到处理最多请求的服务器（困难）

- **题设** ：
  你有 `k` 个服务器，编号为 `0` 到 `k-1` ，它们可以同时处理多个请求组。每个服务器有无穷的计算能力但是 不能同时处理超过一个请求 。请求分配到服务器的规则如下：

     - 第 `i` （序号从 `0` 开始）个请求到达。
     - 如果所有服务器都已被占据，那么该请求被舍弃（完全不处理）。
     - 如果第 `(i % k)` 个服务器空闲，那么对应服务器会处理该请求。
     - 否则，将请求安排给下一个空闲的服务器（服务器构成一个环，必要的话可能从第 `0` 个服务器开始继续找下一个空闲的服务器）。比方说，如果第 `i` 个服务器在忙，那么会查看第 `(i+1)` 个服务器，第 `(i+2)` 个服务器等等。

  给你一个 **严格递增** 的正整数数组 `arrival` ，表示第 `i` 个任务的到达时间，和另一个数组 `load` ，其中 `load[i]` 表示第 `i` 个请求的工作量（也就是服务器完成它所需要的时间）。你的任务是找到 **最繁忙的服务器** 。最繁忙定义为一个服务器处理的请求数是所有服务器里最多的。
  请你返回包含所有 **最繁忙服务器** 序号的列表，你可以以任意顺序返回这个列表。

  **示例一：**
  ![](./笔记图示/Question1606_1.png)

  ```
  输入：k = 3, arrival = [1,2,3,4,5], load = [5,2,3,3,3] 
  输出：[1] 
  解释：
  所有服务器一开始都是空闲的。
  前 3 个请求分别由前 3 台服务器依次处理。
  请求 3 进来的时候，服务器 0 被占据，所以它被安排到下一台空闲的服务器，也就是服务器 1 。
  请求 4 进来的时候，由于所有服务器都被占据，该请求被舍弃。
  服务器 0 和 2 分别都处理了一个请求，服务器 1 处理了两个请求。所以服务器 1 是最忙的服务器。
  ```

  **示例二：**

  ```
  输入：k = 3, arrival = [1,2,3,4], load = [1,2,1,2]
  输出：[0]
  解释：
  前 3 个请求分别被前 3 个服务器处理。
  请求 3 进来，由于服务器 0 空闲，它被服务器 0 处理。
  服务器 0 处理了两个请求，服务器 1 和 2 分别处理了一个请求。所以服务器 0 是最忙的服务器。
  ```

  **示例三：**

  ```
  输入：k = 3, arrival = [1,2,3], load = [10,12,11]
  输出：[0,1,2]
  解释：每个服务器分别处理了一个请求，所以它们都是最忙的服务器。
  ```

  **示例四：**

  ```
  输入：k = 3, arrival = [1,2,3,4,8,9,10], load = [5,2,10,3,1,2,2]
  输出：[1]
  ```

  **示例五：**

  ```
  输入：k = 1, arrival = [1], load = [1]
  输出：[0]
  ```

  **提示：**

     - `1 <= k <= 10^5`
     - `1 <= arrival.length, load.length <= 10^5`
     - `arrival.length == load.length`
     - `1 <= arrival[i], load[i] <= 10^9`
     - `arrival` 保证 **严格递增** 。

- **解题思路**：
  **模拟 + 双优先队列**
  将空闲服务器的编号都放入一个有序集合 available 中，将正在处理请求的服务器的处理结束时间和编号都放入一个优先队列 busy 中，优先队列满足队首的服务器的处理结束时间最小，用一个数组 requests 记录对应服务器处理的请求数目。
  假设当前到达的请求为第 i 个，如果 busy 不为空， 那么我们判断 busy 的队首对应的服务器的结束时间是否小于等于当前请求的到达时间 arrival[i]，如果是，那么我们将它从 busy 中移除，并且将该服务器的编号放入 available 中，然后再次进行判断。如果 available 为空，那么该请求被丢弃；否则查找 available 中大于等于 i mod k 的第一个元素，如果查找成功，那么将它作为处理请求的服务器，否则将 available 中编号最小的服务器作为处理请求的服务器。设处理请求的服务器的编号为 j，那么将 requests[j] 加 1，并且将该服务器从 available 移除，然后将服务器 j 放入 busy 中，对应的处理结束时间为 arrival[i] + load[i]。
  获取 requests 的最大值 maxRequest，遍历 requests 数组，对于每个下标 i，如果 requests[i] = maxRequest，那么将编号 i 加入结果中。
  上述的 available 也可以用优先队列实现。
  设在第 i 个请求到来时，编号为 id 的服务器已经处理完请求，那么将 id 从 busy 中移除，并放入一个不小于 i 且同余于 id 的编号，这样就能在保证 available 中，编号小于 i mod k 的空闲服务器能排到编号不小于 i mod k 的空闲服务器后面。

- **参考代码**：

  ```java
  class Solution {
      public List<Integer> busiestServers(int k, int[] arrival, int[] load) {
          PriorityQueue<Integer> available = new PriorityQueue<Integer>((a, b) -> a - b);
          for (int i = 0; i < k; i++) {
              available.offer(i);
          }
          PriorityQueue<int[]> busy = new PriorityQueue<int[]>((a, b) -> a[0] - b[0]);
          int[] requests = new int[k];
          for (int i = 0; i < arrival.length; i++) {
              while (!busy.isEmpty() && busy.peek()[0] <= arrival[i]) {
                  int id = busy.peek()[1];
                  busy.poll();
                  available.offer(i + ((id - i) % k + k) % k); // 保证得到的是一个不小于 i 的且与 id 同余的数
              }
              if (available.isEmpty()) {
                  continue;
              }
              int server = available.poll() % k;
              requests[server]++;
              busy.offer(new int[]{arrival[i] + load[i], server});
          }
          int maxRequest = Arrays.stream(requests).max().getAsInt();
          List<Integer> ret = new ArrayList<Integer>();
          for (int i = 0; i < k; i++) {
              if (requests[i] == maxRequest) {
                  ret.add(i);
              }
          }
          return ret;
      }
  }
  ```


### 954.二倍数对数组（中等）

- **题设** ：
  给定一个长度为偶数的整数数组 `arr`，只有对 `arr` 进行重组后可以满足 “对于每个 `0 <= i < len(arr) / 2`，都有 `arr[2 * i + 1] = 2 * arr[2 * i]`” 时，返回 `true`；否则，返回 `false`。
  示例一：

  ```
  输入：arr = [3,1,3,6]
  输出：false
  ```

  示例二：

  ```
  输入：arr = [2,1,2,6]
  输出：false
  ```

  示例三：

  ```
  输入：arr = [4,-2,2,-4]
  输出：true
  解释：可以用 [-2,-4] 和 [2,4] 这两组组成 [-2,-4,2,4] 或是 [2,4,-2,-4]
  ```

  提示：

     - `0 <= arr.length <= 3 * 10^4`
     - `arr.length` 是偶数
     - `-10^5 <= arr[i] <= 10^5`

- **解题思路**：
  **哈希表 + 排序**
  设 arr 的长度为 n，题目本质上是问 arr 能否分成 n/2 对元素，每对元素中一个数是另一个数的两倍。
  设 cnt[x] 表示 arr 中 x 的个数。
  对于 arr 中的 0，它只能与 0 匹配。如果 cnt[0] 是奇数，那么必然无法满足题目要求。
  去掉 arr 中的 0。设 x 为 arr 中绝对值最小的元素，由于没有绝对值比 x 更小的数，因此 x 只能与 2x 匹配。如果此时 cnt[2x]<cnt[x]，那么会有部分 x 无法找到它的另一半，即无法满足题目要求；否则将所有 x 和 cnt[x] 个 2x 从 arr 中去掉，继续判断剩余元素是否满足题目要求。不断重复此操作，如果某个时刻 arr 为空，则说明 arr 可以满足题目要求。
  代码实现时，我们可以用一个哈希表来统计 cnt，并将其键值按绝对值从小到大排序，然后模拟上述操作，去掉元素的操作可以改为从 cnt 中减去对应值。

- **参考代码**：

  ```java
  class Solution {
      public boolean canReorderDoubled(int[] arr) {
          Map<Integer, Integer> cnt = new HashMap<Integer, Integer>();
          for (int x : arr) {
              cnt.put(x, cnt.getOrDefault(x, 0) + 1);
          }
          if (cnt.getOrDefault(0, 0) % 2 != 0) {
              return false;
          }
  
          List<Integer> vals = new ArrayList<Integer>();
          for (int x : cnt.keySet()) {
              vals.add(x);
          }
          Collections.sort(vals, (a, b) -> Math.abs(a) - Math.abs(b));
  
          for (int x : vals) {
              if (cnt.getOrDefault(2 * x, 0) < cnt.get(x)) { // 无法找到足够的 2x 与 x 配对
                  return false;
              }
              cnt.put(2 * x, cnt.getOrDefault(2 * x, 0) - cnt.get(x));
          }
          return true;
      }
  }
  ```
