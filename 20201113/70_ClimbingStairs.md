### 70.爬楼梯

n阶楼梯，每次可以爬 1 或 2 个台阶，共有多少种不同的方式可以爬到楼顶？

递归树解题思路：

```
初始状态为S(n)，走一步到S(n-1)，走两步到S(n-2)，直到递归树底部的base case: S(1) && S(0)
从子问题的结果不断向上返回得到父问题的解，调用栈的深度为n，空间复杂度为O(n)，时间复杂度最坏为O(2^n)
存在问题：
	子问题上存在重复项，即该递归树中存在相同的子树。
```

暂存表递归树法解题思路：

```
为避免重复，可以将子问题的结果存放到哈希表结构中或者数组结构中，每次进行递归向下操作时若在暂存表中发现存在情况则不进入相同的递归，去除相同的子项后得到的时间复杂度为O(n)，空间复杂度为O(n)
```

动态规划解题思路：

```
递推式：S(n) = S(n-1) + S(n-2)
base case:S(0) && S(1)
通过dp数组记录中间子问题的解
存在问题：
	dp[i] = dp[i-1] + dp[i-2] 仅和两个项相关无需记录所有的值
```

```java
//动态规划
public class Sloution{
    public int climbStairs(int n){
        int[] dp = new int[n];
        dp[0] = dp[1] = 1;
        for(int i = 2; i < n; i++){
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[n];
    }
}
```

压缩空间优化思路：

```
通过两个指针记录当前节点的结果
```

```java
//动态规划优化
public class Sloution{
    public int climbStairs(int n){
        int p = 0;
        int q = 1;
        int r = 1;
        for(int i = 2; i < n; i++){
            p = q;
            q = r;
            r = p + q;
        }
        return r;
    }
}
```

