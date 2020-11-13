### 121.买卖股票的最佳时机

给定数组，其中i元素对应股票第i天的价格，通过设计算法完成一次交易实现能获取的最大利润

```java
//Method 1:Violent exhaustion
//For per interval:max[prices[j]-prices[i]]
public class Solution{
	public int maxProfit(int[] prices){
        int maxProfit = 0;
        for(int i = 0; i < prices.length() - 1; i++){
            for(int j = i + 1;j < prices.length(); j++){
                if((prices[j] - prices[i]) > maxPrices){
                    maxPrices = prices[j] - prices[i];
                }
            }
        }
        return maxProfit;
    }
}
```

```java
//Method 2:Traverse
//选择当前点为最小值点，依次向后遍历，若取到的值小于最小值点则进行交换，若取到的值大于最小值点则计算利润是否大于当前最大利润值
public class Solution{
    public int maxProfile(int[] prices){
        int minValue = Integer.MAX_VALUE;
        int maxProfit = 0;
        for(int i = 0; i < prices.length(); i++){
            if(prices[i] < minValue){
                minValue = prices[i];
            }else if(prices[i] - minValue > maxProfit){
                maxProfit = prices[i] - minValue;
            }
        }
        return maxProfit;
    }
}
```

