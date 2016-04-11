---
title: "Facebook粉絲團分析（分析專頁：蔡英文）"
output: github_document
---

分析蔡英文粉絲團，資料區間為2016/01/01~2016/04/10

## 讀取蔡英文粉絲團資料

```{r results='hide',message=FALSE, warning=FALSE}
if (!require('Rfacebook')){
    install.packages("Rfacebook")
    library(Rfacebook)
}
```

```{r}
token<-'CAACEdEose0cBAFZAXgwK79ndWBUlnQdaMbGLZAMa7OE4Mpe4xJYRNF29UJGJzt9w4DpCiOCje3TFAssrqb2pkzogej5mb4rGMTAXDNCJvCvBsWc9HGJITy0ZCedlYZBPAr3pmQ4C6duCDUCWoPiiKzZCsbOzMKRKkZBVCyP3R4AZCkpZCWLW47IMqljPQcDZCjAyZCNZCNxOSWYEAZDZD'
totalPage<-NULL
lastDate<-Sys.Date()
DateVectorStr<-as.character(seq(as.Date("2016-01-01"),lastDate,by="5 days"))
for(i in 1:(length(DateVectorStr)-1)){
    tempPage<-getPage("tsaiingwen", token,
                      since = DateVectorStr[i],until = DateVectorStr[i+1])
    totalPage<-rbind(totalPage,tempPage)
}
nrow(totalPage)
```
從2016/01/01到現在蔡英文粉絲團一共有212篇文章

## 每日發文數分析

分析蔡英文粉絲團每天的發文數，首先先把時區轉成台灣時區(GMT+8)，並且將結果以表格呈現：

```{r}
totalPage$datetime<-as.POSIXct(totalPage$created_time,format="%Y-%m-%dT%H:%M:%S+0000",tz="GMT")#2016-04-10T15:05:36+0000
totalPage$dateTPE<-format(totalPage$datetime,"%Y-%m-%d",tz="Asia/Taipei")#2016-04-10
totalPage$weekdays<-weekdays(as.Date(totalPage$dateTPE))

PostCount<-aggregate(id~dateTPE,totalPage,length)
library(knitr)
kable(head(PostCount[order(PostCount$id,decreasing = T),]))
```

從表上可以看出，01/15的貼文數是最多的(8篇)，應該是因為他正在幫01/16的總統大選催票；其次的數個日期都是在01/16之前。

## 每日Likes數分析

分析蔡英文粉絲團每天發文的Likes數，一樣將結果變成表格：

```{r}
LikesCount<-aggregate(likes~dateTPE,totalPage,mean)
library(knitr)
kable(head(LikesCount[order(LikesCount$likes,decreasing = T),]))
```


## 每日Comments數分析

分析蔡英文粉絲團每天發文的Comments數，一樣將結果變成表格：

```{r}
CommentsCount<-aggregate(comments~dateTPE,totalPage,mean)
library(knitr)
kable(head(CommentsCount[order(CommentsCount$comments,decreasing = T),]))
```


## 每日shares數分析

分析蔡英文粉絲團每天發文的Shares數，一樣將結果變成表格：

```{r}
SharesCount<-aggregate(Shares~dateTPE,totalPage,mean)
library(knitr)
kable(head(SharesCount[order(SharesCount$shares,decreasing = T),]))
```
