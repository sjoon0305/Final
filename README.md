# Final

---
title: "final project"
output: html_document
date: "2023-06-22"
---

## 금융통계 기말과제 증권 분석
우선 KRX 정보데이터시스템에서 3,6개월 등락율 데이터를 다운받는다.
다음에 다운받은 데이터를 data.frame으로 변환한다.
```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = TRUE)
```
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
