# 分治

将原问题递归地分成若干子问题，直到子问题满足边界条件，停止递归。将子问题逐个击破，将已经解决的子问题合并，最后，算法会层层合并得到原问题的答案。

#### 步骤

分：分解成小问题

治：小问题逐个击破

合：已解决的小问题逐层合并

#### 适用情况

- 原问题的计算复杂度随着问题的规模增加而增加
- 原问题能够被分解成更小的子问题
- 子问题的结构和性质与原问题一样，并且相互独立，子问题不包含公共的子子问题
- 原问题分解出的子问题的解可以合并为该问题的解

### 问题1

https://leetcode-cn.com/problems/powx-n/

#### 问题1描述

实现 pow(x, n) ，即计算 x 的 n 次幂函数。

示例 1:

输入: 2.00000, 10
输出: 1024.00000

示例 2:

输入: 2.10000, 3
输出: 9.26100

示例 3:

输入: 2.00000, -2
输出: 0.25000
解释: 2-2 = 1/22 = 1/4 = 0.25
说明:

-100.0 < x < 100.0
n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

#### 关键点

分治法减少计算次数

#### 代码

```java
class Solution {
    public double myPow(double x, int n) {
        if(n == 1){
            return x;
        }
        if(n == 0){
            return 1;
        } 
        if(n == -1){
            return 1/x;
        }
        double half = myPow(x, n / 2);
        double rest = myPow(x, n % 2);
        return rest * half * half;
    }
}
```



### 问题2

https://leetcode-cn.com/problems/maximum-subarray/

#### 问题2描述

给定一个整数数组 nums ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

示例:

输入: [-2,1,-3,4,-1,2,1,-5,4]
输出: 6
解释: 连续子数组 [4,-1,2,1] 的和最大，为 6。

#### 关键点

将给定的数组分解到单个数字，然后进行合并，分别需要得到左边的最大和，右边的最大和以及跨中心的最大和，进行比较，返回最大值。

#### 代码

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null){
            return 0;
        }
        int length = nums.length;
        if(length==1){
            return nums[0];
        }
        return divide_method(nums,0,length-1);
    }

    public int divide_method(int[] nums, int start, int end){
        //递归结束条件
        if(start == end){
            return nums[start];
        }
        //计算中间值
        int middle = (start+end)/2;
        int leftMax = divide_method(nums,start,middle);
        int rightMax = divide_method(nums,middle+1,end);
        //计算包含左侧子序列最后一个元素的子序列最大值
        int leftCrossMax = Integer.MIN_VALUE;
        int leftCrossSum = 0;
        for(int i = middle ; i>= start; i--){
            leftCrossSum += nums[i];
            leftCrossMax = Math.max(leftCrossSum,leftCrossMax);
        }
        //计算包含右侧子序列最后一个元素的子序列最大值
        int rightCrossMax = nums[middle+1];
        int rightCrossSum = 0;
        for(int i = middle+1; i<=end;i++ ){
            rightCrossSum += nums[i];
            rightCrossMax = Math.max(rightCrossSum,rightCrossMax);
        }
        //计算跨中心的子序列最大值
        int crossMax = leftCrossMax + rightCrossMax;
        //比较三者，返回最大值
        return Math.max(crossMax,Math.max(leftMax,rightMax));
    }
}
```



### 问题3

https://leetcode-cn.com/problems/majority-element/

#### 问题3描述

给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

示例 1:

输入: [3,2,3]
输出: 3

示例 2:

输入: [2,2,1,1,1,2,2]
输出: 2

#### 关键点

多数元素必定是左边的多数元素或右边的多数元素中的一个。

#### 代码

```java
class Solution {
    private int countInRange(int[] nums, int num, int lo, int hi) {
        int count = 0;
        for (int i = lo; i <= hi; i++) {
            if (nums[i] == num) {
                count++;
            }
        }
        return count;
    }

    private int majorityElementRec(int[] nums, int lo, int hi) {
        //递归结束条件
        if (lo == hi) {
            return nums[lo];
        }
        //计算中间值
        int mid = (hi-lo)/2 + lo;
        int left = majorityElementRec(nums, lo, mid);
        int right = majorityElementRec(nums, mid+1, hi);
        //如果两边最多的元素相同，则直接返回
        if (left == right) {
            return left;
        }
        //否则，遍历数组，得到左右两个最多元素的数量
        int leftCount = countInRange(nums, left, lo, hi);
        int rightCount = countInRange(nums, right, lo, hi);
        //返回多数元素
        return leftCount > rightCount ? left : right;
    }

    public int majorityElement(int[] nums) {
        return majorityElementRec(nums, 0, nums.length-1);
    }
}
```

