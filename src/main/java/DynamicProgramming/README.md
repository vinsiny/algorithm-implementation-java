## 动态规划（dynamic programming）

### 动态规划和分治法

相同点：都是通过组合子问题来求解原问题；

不同点：
- 1.动态规划应用于子问题重叠的情况，即不同子问题具有公共的子子问题（子问题的求解是递归进行的，将其划分为更小的子子问题）。
分治法将问题划分为互不相交的子问题，递归的求解子问题，再将它们组合起来，求出原问题的解。

- 2.动态规划对每个子问题只求解一次，将其解保存在一个表格中，从而无需每次重复计算相同的子问题。动态规划通常用来求解**最优化问题**（optimization problem）.


### 算法设计步骤

1. 刻画一个最优解结构特征。
2. 递归地定义最优解的值。
3. 计算最优解的值，通常采用自底向上的方法。
4. 利用计算出的信息构造一个最优解。


### 案例1：钢条切割

条件：有一条长钢条，可将其切割为较短钢条出售，切割工序本身没有成本，不同长度短钢条的售价如下表，求长度为 n 的钢条的最佳切割后收益；

长度i             | 1    |  2  |  3  |  4  |  5  |  6  |  7  |  8  |  9  |  10
:----:           | :---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:
价格p<sub>i</sub> | 1    | 5   |  8  |  9  |  10 |  17 |  17 |  20 |  24 |  30


举例来说：
当长度 n = 4 时，最优收益是10，即切割成两段长度为2的短钢条，收益最大；
长度 n = 10 时，最优收益是30，即不切割收益最大；

#### 解法1 —— 朴素递归方法

将钢条从左边切割下长度为 i 的一段，只对右边剩下的长度为 n - i 的一段继续进行切割（递归求解），对左边的一段则不再进行切割，即问题分解的方式为：将长度为 n 的钢条分解为左边开始一段，以及剩余部分继续分解的结果。
这样，不做任何切割的方案就可以描述为：第一段的长度为 n， 收益为 p，剩余部分长度为 0，对应的收益为 r<sub>0</sub> = 0。

自顶向下递归实现：

```java
public class CutRod {
  /**
   * 朴素递归方案
   * 包含重复的计算，n 每增加 1，运算次数几乎增加一倍；
   * @param p 
   * @param n
   * @return
   */
  public static int cutRod(int[] p, int n){
    if (n == 0) {
      return 0;
    }
    int q = Integer.MIN_VALUE;
    for (int i = 1; i <= n; i++) {
      q = Math.max(q, p[i] + cutRod(p, n -i));
    }
    return q;
  }
}
```

### 动态规划原理

适合应用动态规划方法求解最优化问题应该具备两个要素：最优子结构和子结构问题重叠。

- 最优子结构： 用动态规划方法求解最优化问题的第一步就是刻画最优解的结构。如果一个问题的最优解包含其子问题的最优解，就称此问题具有最优子结构性质。

在发掘最优子结构性质的过程中，实际上遵循了如下通用模式：

1. 证明问题最优解的第一个组成部分是作出一个选择，例如，选择钢条第一次切割位置，选择矩阵链的划分位置等。作出这次选择会产生一个或多个待解子问题。

2. 对于一个给定的问题，在其可能的第一步选择中，假定已经知道那种选择才会得到最优解，现在并不关心这种选择具体是如何得到的，只是假定已经知道了这种选择。

3. 给定可获得的最优解的选择后，确定这次选择会产生哪些子问题，以及如何最好地刻画子问题空间。

4. 利用 “剪切-粘贴”（cut-and-paste）技术证明：作为构成原问题最优解的组成部分，每个子问题的解就是它本身的最优解。

对于不同问题领域，最优子结构的不同体现在两个方面：

 1. 原问题的最优解中涉及多少个子问题，以及，
 2. 在确定最优解使用哪些子问题时，我们需要考察多少种选择。

- 重叠子问题： 子问题空间必须足够”小“，即问题的递归算法会反复的求解相同的子问题，而不是一直生成新的子问题。如果递归算法反复求解相同的子问题，就称为最优化问题具有重叠子问题（overlapping subproblem）性质。与之相对的，适合用分治法求解的问题通常在递归的每一步都生成全新的子问题。

一个问题是否适合用动态规划求解同时依赖于子问题的无关性和重叠性，这看起来很奇怪，虽然这两个要求听起来似乎是矛盾的，但他们描述的是不同的概念，而不是同一个坐标轴的两个点，两个子问题如果不共享资源，它们就是独立的，而重叠是指两个子问题实际上是同一个子问题，只是作为不同问题的子问题出现而已。