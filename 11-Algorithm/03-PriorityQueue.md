# PriorityQueue

- 优先队列，正常入、按照优先级出

- 实现机制

  > 1、Heap（Binary、Binomial、Fibonacci）
  >
  > 2、Binary Search Tree

<img src="img/image-20220728100019511.png" alt="image-20220728100019511" style="zoom:50%;" />

## [703. 数据流中的第 K 大元素](https://leetcode.cn/problems/kth-largest-element-in-a-stream/)

> 1、保存前K个最大的值，要进行排序；（N.K.logK）
>
> 2、使用优先队列（PriorityQueue在java中默认就是小顶堆）；（log2 k）

```java
class KthLargest {

    final PriorityQueue<Integer> q;
    final int k;

    public KthLargest(int k, int[] nums) {
        this.k = k;
        q = new PriorityQueue<>(k);
        for (int i = 0; i < nums.length; i++) {
            add(nums[i]);
        }
    }

    public int add(int val) {
        if (q.size() < k) {
            q.offer(val);
        } else if (q.peek() < val) {
            q.poll();
            q.offer(val);
        }
        return q.peek();
    }
}
```



## [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/)

> 思路：
>
> 1. 使用大顶堆；（N.logK）
> 2. 使用Queue，双端队列-》deque；（O(N)）

>使用双端队列，过程演示：
>
>输入: nums = [1,3,-1,-3,5,3,6,7], 和 k = 3
>输出: [3,3,5,5,6,7]
>
>解释过程中队列中都是具体的值，方便理解，具体见代码。
>初始状态：L=R=0,队列:{}
>i=0,nums[0]=1。 队列为空,直接加入。队列：{1}
>i=1,nums[1]=3。 队尾值为1，3>1，弹出队尾值，加入3。队列：{3}
>i=2,nums[2]=-1。队尾值为3，-1<3，直接加入。队列：{3,-1}。此时窗口已经形成，L=0,R=2，result=[3]
>i=3,nums[3]=-3。队尾值为-1，-3<-1，直接加入。队列：{3,-1,-3}。队首3对应的下标为1，L=1,R=3，有效。result=[3,3]
>i=4,nums[4]=5。 队尾值为-3，5>-3，依次弹出后加入。队列：{5}。此时L=2,R=4，有效。result=[3,3,5]
>i=5,nums[5]=3。 队尾值为5，3<5，直接加入。队列：{5,3}。此时L=3,R=5，有效。result=[3,3,5,5]
>i=6,nums[6]=6。 队尾值为3，6>3，依次弹出后加入。队列：{6}。此时L=4,R=6，有效。result=[3,3,5,5,6]
>i=7,nums[7]=7。 队尾值为6，7>6，弹出队尾值后加入。队列：{7}。此时L=5,R=7，有效。result=[3,3,5,5,6,7]
>
>作者：hanyuhuang	[链接](https://leetcode.cn/problems/sliding-window-maximum/solution/shuang-xiang-dui-lie-jie-jue-hua-dong-chuang-kou-2/)

```java

//[1,3,1,2,0,5]
// 1   3   -1  -3  5   3   6   7

class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length < 2) return nums;
        // 双向队列 保存当前窗口最大值的数组位置 保证队列中数组位置的数值按从大到小排序
        LinkedList<Integer> queue = new LinkedList();
        // 结果数组
        int[] result = new int[nums.length - k + 1];
        // 遍历nums数组
        for (int i = 0; i < nums.length; i++) {
            // 保证从大到小 如果前面数小则需要依次弹出，直至满足要求
            while (!queue.isEmpty() && nums[queue.peekLast()] <= nums[i]) {
                queue.pollLast();
            }
            // 添加当前值对应的数组下标
            queue.addLast(i);
            // 判断当前队列中队首的值是否有效
            if (queue.peek() <= i - k) {
                queue.poll();
            }
            // 当窗口长度为k时 保存当前窗口中最大值
            if (i + 1 >= k) {
                result[i + 1 - k] = nums[queue.peek()];
            }
        }
        return result;
    }
}

```

