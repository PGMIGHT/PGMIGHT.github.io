---
title: "力扣刷题，2022年4月"
description: "记录一下去年四月的做力扣题的一些思路和别人的解答"
date: "2023-08-17T05:28:05+08:00"
publishDate: "2023-08-17T05:28:05+08:00"
---

### 780.到达终点（困难）

- **题设** ：
  给定四个整数 `sx` , `sy` ，`tx` 和 `ty`，如果通过一系列的转换可以从起点 `(sx, sy)` 到达终点 `(tx, ty)`，则返回 `true`，否则返回 `false`。
  从点 `(x, y)` 可以转换到 `(x, x+y)`  或者 `(x+y, y)`。
  **示例一：**

  ```
  输入: sx = 1, sy = 1, tx = 3, ty = 5
  输出: true
  解释:
  可以通过以下一系列转换从起点转换到终点：
  (1, 1) -> (1, 2)
  (1, 2) -> (3, 2)
  (3, 2) -> (3, 5)
  ```

  **示例二：**

  ```
  输入: sx = 1, sy = 1, tx = 2, ty = 2 
  输出: false
  ```

  **示例三：**

  ```
  输入: sx = 1, sy = 1, tx = 1, ty = 1 
  输出: true
  ```

  **提示：**

     - `1 <= sx, sy, tx, ty <= 10^9`

- **解题思路**：
  给定的 `(sx, sy)` 的数据范围为 `[1, 10^9]`（即均为正整数），且每次转换，只能将另外一维的数值累加到当前维，因此对于每一维的数值而言，随着转换次数的进行，呈（非严格）递增趋势，再结合起始值为正整数，可知在转换过程中均不会出现负数。
  由此得知从 `(tx,ty)` 到 `(sx,sy)` 的转换过程唯一确定：总是取较大数减去较小数来进行反推（否则会出现负数）。
  但即使反向转换唯一确定，数据范围为 `10^9` ，线性模拟仍会超时。
  我们考虑将「相同操作的连续段转换动作」进行合并，在某次反向转换中，如果有 `tx < ty`，我们会将 `(tx, ty)` 转换为 `(tx, ty - tx)`，若相减完仍有 `tx < ty - tx`，该操作会继续进行，得到 `(tx, ty - 2 * tx)` ，直到不满足 `tx < ty - k * tx`，其中 `k` 为转换次数。
  即对于一般性的情况而言，`(tx, ty)` 中的较大数会一直消减到「与较小数的余数」为止。
  因此我们可以先使用 `O(logmax(tx,ty))` 的复杂度将其消减到不超过 `(sx, sy)` 为止。此时如果消减后的结果 `(tx, ty)` 任一维度小于 `(sx, sy)`，必然不能进行转换，返回 `False`；如果任一维度相等（假定是 `x` 维度），则检查另一维度（`y` 维度）的差值，是否能够由当前维度（`x` 维度）拼凑而来。

- **参考代码**：

  ```java
  class Solution {
      public boolean reachingPoints(int sx, int sy, int tx, int ty) {
          while (sx < tx && sy < ty) {
              if (tx < ty) ty %= tx;
              else tx %= ty;
          }
          if (tx < sx || ty < sy) return false;
          return sx == tx ? (ty - sy) % tx == 0 : (tx - sx) % ty == 0;
      }
  }
  ```

- **总结**：正向不行时可以尝试逆向思考。



### 357.统计各位数字都不同的数字个数（中等）

- **题设** ：
  给你一个整数 `n` ，统计并返回各位数字都不同的数字 `x` 的个数，其中 `0 <= x < 10^n` 。
  **示例一：**

  ```
  输入：n = 2
  输出：91
  解释：答案应为除去 11、22、33、44、55、66、77、88、99 外，在 0 ≤ x < 100 范围内的所有数字。 
  ```

  **示例二：**

  ```
  输入：n = 0
  输出：1
  ```

  **提示：**

     - `0 <= n <= 8`

- **解题思路**：
  **排列组合**
  首先考虑两种边界情况：

     - 当 n = 0 时，10≤x<1，x 只有 1 种选择，即 0。
     - 当 n = 1 时，100≤x<10，x 有 10 种选择，即 0∼9。

  当 n = 2 时，1000≤x<100，x 的选择可以由两部分构成：只有一位数的 x 和有两位数的 x。只有一位数的 x 可以由上述的边界情况计算。有两位数的 x 可以由组合数学进行计算：第一位的选择有 9 种，即 1∼9，第二位的选择也有 9 种，即 0∼9 中除去第一位的选择。
  更一般地，含有 d （2≤d≤10）位数的各位数字都不同的数字 x 的个数可以由公式 
  $$
  9 \times A_{9}^{d-1}
  $$
  计算。再加上含有小于 d 位数的各位数字都不同的数字 x 的个数，即可得到答案。

- **参考代码**：

  ```java
  class Solution {
      public int countNumbersWithUniqueDigits(int n) {
          if (n == 0) {
              return 1;
          }
          if (n == 1) {
              return 10;
          }
          int res = 10, cur = 9;
          for (int i = 0; i < n - 1; i++) {
              cur *= 9 - i;
              res += cur;
          }
          return res;
      }
  }
  ```

- **总结**：



### 380.O(1) 时间插入、删除和获取随机元素（中等）

- **题设** ：
  实现`RandomizedSet` 类：

     - `RandomizedSet()` 初始化 `RandomizedSet` 对象
     - `bool insert(int val)` 当元素 `val` 不存在时，向集合中插入该项，并返回 `true` ；否则，返回 `false` 。
     - `bool remove(int val)` 当元素 `val` 存在时，从集合中移除该项，并返回 `true` ；否则，返回 `false` 。
     - `int getRandom()` 随机返回现有集合中的一项（测试用例保证调用此方法时集合中至少存在一个元素）。每个元素应该有 **相同的概率** 被返回。

  你必须实现类的所有函数，并满足每个函数的 **平均** 时间复杂度为 `O(1)` 。
  **示例：**

  ```
  输入
   ["RandomizedSet", "insert", "remove", "insert", "getRandom", "remove", "insert", "getRandom"]
   [[], [1], [2], [2], [], [1], [2], []]
   输出
   [null, true, false, true, 2, true, false, 2]
  
   解释
   RandomizedSet randomizedSet = new RandomizedSet();
   randomizedSet.insert(1); // 向集合中插入 1 。返回 true 表示 1 被成功地插入。
   randomizedSet.remove(2); // 返回 false ，表示集合中不存在 2 。
   randomizedSet.insert(2); // 向集合中插入 2 。返回 true 。集合现在包含 [1,2] 。
   randomizedSet.getRandom(); // getRandom 应随机返回 1 或 2 。
   randomizedSet.remove(1); // 从集合中移除 1 ，返回 true 。集合现在包含 [2] 。
   randomizedSet.insert(2); // 2 已在集合中，所以返回 false 。
   randomizedSet.getRandom(); // 由于 2 是集合中唯一的数字，getRandom 总是返回 2 。
  ```

  **提示：**

     - `-2^31 <= val <= 2^31 - 1`
     - 最多调用 `insert`、`remove` 和 `getRandom` 函数 `2 * 10^5` 次
     - 在调用 `getRandom` 方法时，数据结构中 **至少存在一个** 元素。


- **解题思路**：
  首先是要以相同概率返回，所以一般是用数组或者list配合Random函数来实现随机平均取值。然后要满足insert和remove的O(1)操作，所以我们会想到HashSet或者是HashMap。但是困难点在于remove的时候要满足原数据在数组中是紧密排列的，即删除数组内部值得时候要保证所有数据还能紧密排列，因此想到将删除位置的值替换为数组中最后一个有效值，这样便可以满足紧密排列。同时，我们需要找到所删除值所在原数组的位置，因此我们可以使用HashMap，key为要新增或是删除的val，value为该数在数组中的下标值。因为要实现将最后一个值赋值到删除位置，所以需要维护一个last变量，记录数组得最后一个值所在的下标。

- **参考代码**：

  ```java
  class RandomizedSet {
  
      HashMap<Integer, Integer> map;
      int[] arr;
      int last = -1;
      int target = -1;
      Random random= new Random();
  
      public RandomizedSet() {
          map = new HashMap<>();
          arr = new int[2*100001];
      }
  
      public boolean insert(int val) {
          if (!map.containsKey(val)) {
              arr[++last] = val;
              map.put(val, last);
              return true;
          } else {
              return false;
          }
      }
  
      public boolean remove(int val) {
          if (map.containsKey(val)) {
              target = map.get(val);
              map.put(arr[last], target);
              arr[target] = arr[last--];
              map.remove(val);
              return true;
          } else {
              return false;
          }
      }
  
      public int getRandom() {
          int randomInt =  random.nextInt(last + 1);
          return arr[randomInt];
      }
  }
  ```
