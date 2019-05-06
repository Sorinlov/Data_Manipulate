# manipulate of dataframe

## 用SQL操作dataframe

### 留存分析
![](https://github.com/Sorinlov/Data_Manipulate/blob/master/datapic/ModifiedPilotStudyRawData%20only%20Retail%20Relay.png)

数据根据UserID, OrderID排序 分别对应用户和此用户购买订单
对用户购买次数编 统计每次购买的人数 每次销售总量
row_number  count  sum 

用sql语句操作dataframe
```R
library(sqldf)
test <- sqldf('select OrderId, UserId, TotalCharges from ModifiedPilotStudyRawData_only_Retail_Relay')

#R中的row_number
library(data.table)
test <- as.data.table(test)
test[, rn := rank(OrderId), by = 'UserId']
#partition by userid order by oderid
#参数为orderid 和 userid rn可变
#若rank()有相同 编号会有重复 如何不重复编号？？
#如何给一列数据编号？？

#number of trantistions and avg of charges
test <- as.data.frame(test)
result <- sqldf('select count(TotalCharges), avg(TotalCharges) from test group by rn')
```

两列错位运算 第一列的第1个&第二列的第2/3个
```R
#transition probibilty
result$tb <- 1
result$tb[seq(2,76)] <- result$`count(TotalCharges)`[seq(2,76)]/result$`count(TotalCharges)`[seq(1,75)]
```
一列错位运算
```R
result$retention[1] <- 1
for (i in seq(2, length(result$tb))){
  result$retention[i] <- result$tb[i] * result$retention[i-1]
}
#for loop效率极低 用R累乘函数 返回向量
result$retentioncumprod <- cumprod(result$tb)
```
累计运算
```R
cumsum(1:5) #累加
cumprod(1:5) #累乘
cummin(l) #最小累计 截止当前序列最小
cummax(l) #最大累计
```



