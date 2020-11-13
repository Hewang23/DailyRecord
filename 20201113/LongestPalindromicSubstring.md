## 动态规划问题

### 问题形式

```
穷举所有可能值取最值
该类型问题一定具有最优子结构
```

```java
//最长回文子串：给定字符串s，找到s中的最长回文子串，假设s最大长度为1000
/**
	动态规划：一个回文字符串去掉两端后仍为回文子串，当长度大于2时其仍为回文串
			即如果s[i,j]=true-->s[i+1,j-1]=true
	状态转移方程：P(i,j)=P(i+1,j-1)^(Si==Sj)
	边界条件：P(i,i)=true   P(i,i+1)=(Si==Si+1)
	Tips：从长度较短的字符串向较长的字符串进行转移
*/
class Solution{
    public String longestPalindrome(String s){
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        String ans = "";
        for(int l = 0; l < n; l++){
            for(int i = 0; i + l < n; ++i){
                int j = i + l;
                if(l == 0){
                    dp[i][j] = true;
                }else if(l == 1){
                    dp[i][j] = s.charAt(i) == s.charAt(j);
                }else{
                    dp[i][j] = dp[i + 1][j - 1] && s.charAt(i) == s.charAt(j);
                }
                if(dp[i][j] && l + 1 > ans.length()){
                    ans = s.substring(i, i + l + 1);
                }
            }
        }
        return ans;
    }
}
```

