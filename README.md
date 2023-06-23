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
```{r}
x <- c(1, 2, 3)
mean(x)
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
```

## 금융통계 기말과제 증권 분석
우선 KRX 정보데이터시스템에서 3,6개월 등락율 데이터를 다운받는다./n
다음에 다운받은 데이터를 data.frame으로 변환한다.

```{r}
library(readxl)
library(dplyr) 
```

The mean of x is `r mean(x)`. 1
## R Markdown

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

```{r cars}
summary(cars)
```

## Including Plots

You can also embed plots, for example:

```{r pressure, echo=FALSE}
plot(pressure)
```

Note that the `echo = FALSE` parameter was added to the code chunk to prevent printing of the R code that generated the plot.
