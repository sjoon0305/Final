# Final

---
title: "final project"
output: html_document
date: "2023-06-22"
---
## 금융통계 기말과제 증권 분석
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```

## 주식 정보를 네이버에서 가져오는 코드
```
library(tidyverse)
library(lubridate)
library(rvest) 


# 총 페이지 수
get_last_page <- function(url) {
  html <- read_html(url, encoding="euc-kr") #인코딩은 한글 깨짐
  
  page_item <- html %>% # node보다는 element 사용
    html_nodes(".pgRR") %>% # 마지막 페이지 검사 눌러서 보면 나옴
    html_nodes("a") %>%
    html_attr("href") %>% 
    str_split("page=") 
  
  last_page <- page_item[[1]][2]
  return(last_page)
}

# 각 종목의 기본 정보  
get_info <- function(url){
  html <- read_html(url, encoding="euc-kr")
  
  종목명 <- html %>%
    html_nodes(".tltle") %>%
    html_text()
  
  code <- html %>%       # 종목 코드
    html_nodes("td") %>%
    html_nodes("a") %>%
    html_attr("href")
  
  code <- code[str_detect(code, "item")] # 코드에서 item 인 줄만 찾아서 분리
  code <- str_sub(code, -6, -1)
  
  종목코드 <- code[c(TRUE, FALSE)]
  
  data <- html %>%
    html_nodes(".number") %>% # 정보창 안에 숫자를 전부 찾는다
    html_text() %>%
    parse_number(na="N/A") #\n \t 같은 정보를 한번에 깔끔하게 정리
  
  data_name <- html %>% # 종목명 현재가 등등을 가져옴
    html_nodes("th") %>%
    html_text() 
  
  var_names <- data_name[3:12]
  
  df <- data.frame(matrix(data, ncol = length(var_names), byrow = T))
  names(df) <- var_names
  df$종목명 <- 종목명   
  df$종목코드 <- 종목코드   
  
  df <- df %>%
    select(종목명, 종목코드, 액면가, 상장주식수, PER, ROE, 현재가, 시가총액, 외국인비율)
  
  return(df)
}

get_stock_info <- function(base_url) {
  last_page <- get_last_page(paste0(base_url, 1))
  
  df <- data.frame() #10번째 데이터마다 잘 나오고 있는지 확인하는것!
  for(page in 1:last_page) {
    if(page %% 10 == 0) print(paste(page, "/", last_page))
    df <- rbind(df, get_info(paste0(base_url, page)))
  }
  return(df)
}

# 최근의 코드로 읽음
kospi<-read.csv("C:/Users/jbnu/Documents/R file/basic/kospi.csv")
kospi <- kospi %>%
  select(종목명, 종목코드, 상장주식수, 현재가, 시가총액)
```
## 

## 금융통계 기말과제 증권 분석
우선 KRX 정보데이터시스템에서 3,6개월 등락율 데이터를 다운받는다.
다음에 다운받은 데이터를 data.frame으로 변환한다.

```{r}
library(readxl)
library(dplyr) 


# 3개월 등락률 데이터 
data <- read_excel("C:/Users/jbnu/Downloads/data_5621_20230610.xlsx")
dt<-data.frame(data)
dt[dt$종목명=="한화에어로스페이스",]

# 6개월 등락률 데이터
data2<-read_excel("C:/Users/jbnu/Downloads/data_2154_20230610.xlsx")
dt2<-data.frame(data2) 
```

## 현재 kospi 200 기업들의 3, 6개월 등락률을 읽는 코드
```
# 3개월 데이터 가져오기
kospi$rate1<-NA
count<-0
for (i in 1:400){
  if (any(dt[,1]==kospi[,2][i])==TRUE){
    kospi[i,]$rate1<-dt[dt[,1]==kospi[,2][i],]$등락률
    count<-count+1
  }
  if (count==200) { # kospi200 기업까지만 조사
    print(i)
    break
  }
}

# 6개월 데이터 가져오기
kospi$rate2<-NA
count<-0
for (i in 1:400){
  if (any(dt2[,1]==kospi[,2][i])==TRUE){
    kospi[i,]$rate2<-dt2[dt2[,1]==kospi[,2][i],]$등락률
    count<-count+1
  }
  if (count==200) { # kospi200 기업까지만 조사
    print(i)
    break
  }
}
```
## 최종 코스피 200의 등락율 rate1:3개월 기준, rate2:6개월 기준
```
kospi200<-kospi[!is.na(kospi$rate1),]
```
등락율 최고,최저 확인하기


# 3개월 등락율 히스토그램
```
hist(kospi200$rate1, breaks = seq(min(kospi200$rate1),max(kospi200$rate1)+1,1),
     xlim = c(min(kospi200$rate1),max(kospi200$rate1)),xaxt = "n")
axis(side = 1, at = seq(-30, 190, by = 10))
abline(v=0,col='red',lwd=2)
```
# 6개월 등락율 히스토그램
```
hist(kospi200$rate2, breaks = seq(min(kospi200$rate2),max(kospi200$rate2)+1,1),
     xlim = c(min(kospi200$rate2),max(kospi200$rate2)),xaxt = "n")
axis(side = 1, at = seq(-60,280 , by = 10))
abline(v=0,col='red',lwd=2)
```

