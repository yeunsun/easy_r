---
title: "제목"
author: 작성자
date: July 30, 2020
output: github_document
---


```{r, include=F}
Sys.setenv("LANGUAGE"="EN")
library(foreign)             # SPSS 파일 로드
library(dplyr)               # 전처리
library(ggplot2)             # 시각화
library(readxl)              # 엑셀 파일 불러오기
```

```{r, warning=F,include=F}

# 데이터 불러오기
raw_welfare <- read.spss(file = "Koweps_hpc10_2015_beta1.sav",
                         to.data.frame=T)

# 복사본 만들기
welfare <- raw_welfare
```


```{R, eval=T,include=F}
welfare <- rename(welfare,
                  sex = h10_g3,
                  birth=h10_g4,
                  marriage=h10_g10,
                  religion=h10_g11,
                  code_job=h10_eco9,
                  income=p1002_8aq1,
                  code_region=h10_reg7)
```




## 2. 성별에 따른 월급 차이 

성별과 월급 두 변수를 검토, 전 처리하여 변수 간의 관계를 분석한다.
### 분석 절차

### 성별 변수 검토 및 전처리 

#### 1. 변수 검토하기
성별 변수의 타입을 파악하고, 각 범주에 몇 명이 있는지 확인

```{r,eval=T}
class(welfare$sex)
table(welfare$sex)

```
#### 2. 전처리

성별 변수의 값 = 1, 남자
성별 변수의 값 = 2, 여자
결측값 = 9
데이터 이상치 검토, 이상치 제외=NA부여

```{r,eval=T}
welfare$sex <- ifelse(welfare$sex == 9, NA, welfare$sex)
table(is.na(welfare$sex))
```

```{r,eval=T}
welfare$sex <- ifelse(welfare$sex == 1, "male","female")
table(welfare$sex)

qplot(welfare$sex)
```

### 월급 변수 검토 및 전처리

#### 1. 변수 검토하기

```{r,eval=T}

class(welfare$income)

summary(welfare$income)
qplot(welfare$income)

```
#### 2. 전처리

```{r,eval=T}

summary(welfare$income)
welfare$income <- ifelse(welfare$income %in% c(0,9999), NA, welfare$income)
table(is.na(welfare$income))
```
### 성별에 따른 월급 차이 분석하기

#### 1. 성별 월급 평균표 만들기

```{r,eval=T}

sex_income <- welfare %>% filter(!is.na(income)) %>% 
group_by(sex) %>% 
summarise(mean_income=mean(income))

sex_income
```


#### 2. 그래프 만들기

```{r,eval=T}
ggplot(data=sex_income, aes(x=sex, y=mean_income)) + geom_col()
```


