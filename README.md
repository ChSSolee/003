## 📌 항공사의 고객 만족도를 높이는 요인분석
> #### 개요 : 항공사 서비스 만족도 데이터의 이진적 반응변수(항공사 서비스 만족 여부)의 오즈에 설명변수가 어떤 영향을 미치는지를 파악.

#

### 1. 제작 기간 & 참여 인원
> #### 2020.11 ~ 2020.12
> #### 개인
> #### 수행 역할 
>> 데이터 전처리, 변수간 상관관계 분석, 다중공선성 파악, 모형생성 및 계수 추정값 분석, 보고서 작성

#

### 2. 사용 프로그램 : R 4.0.0
#### 사용 라이브러리 : 
> library(ggplot2)  
> library(GGally)

#

### 3. [R코드](https://github.com/ChSSolee/003/blob/main/%ED%95%AD%EA%B3%B5%EC%82%AC%EC%9D%98%20%EA%B3%A0%EA%B0%9D%20%EB%A7%8C%EC%A1%B1%EB%8F%84%EB%A5%BC%20%EB%86%92%EC%9D%B4%EB%8A%94%20%EC%9A%94%EC%9D%B8%EC%97%90%20%EB%8C%80%ED%95%98%EC%97%AC.Rmd)


## 1. 서론

<br/>

### 분석을 하게된 동기나 배경 
- 이번 학기 학습한 로지스틱 회귀를, 방대한 양의 비즈니스 데이터에 직접 적용을 해봄으로서, 반응변수(항공사 서비스 만족 여부에 대한 오즈)에 설명변수가 어떤 영향을 미치는지를 파악하기 위해 본 주제를 선택.
- 더 나아가 로지스틱 회귀가 가진 예측 및 분류기로서의 특성 또한, 해당 분석을 통하여 직접 확인하고자 함.

<br/>

### 수집한 자료의 출처 및 성격
- "US Airline Passenger Satisfaction"
- 수집한 자료는 2015년 미국의 항공사를 이용한 12만 명 이상의 고객들의 서비스 만족도에 대한 데이터로서, 항공사와 고객의 이름은 기업정보 및 개인정보의 이유로 식별할 수 없음.

<br/>


### 변수
| | | |
| -- | -- | -- |
| Satisfaction | 고객 만족 여부 | 명목형 (1 = 만족 / 0 = 중립 또는 불만족) |
| Gender | 고객의 성별 | 명목형 (Female = 여성 / Male = 남성) |
| Customer.Type | 고객 유형 | 명목형 (disloyal Customer = 충성도가 낮은 고객 / Loyal Customer = 충성도가 높은 고객 ) |
| Age | 고객의 연령 | 수치형 |
| Type.of.Travel | 고객의 탑승 목적 / 명목형 (Personal Travel = 개인적 목적 / Business Travel = 비즈니스 목적) |
| Class | 좌석 유형 | 명목형 (Business = 비즈니스 석 / Eco Plus = 이코노미 플러스 / Eco = 이코노미) |
| Flight.Distance | 비행 거리(노트) | 수치형 |
| Seat.comfort | 착석감 | 순서형 (0 ~ 5) |
| Dep.Arr.time.convenient | 도착/출발 편리성 | 순서형 (0 ~ 5) ||
| Food.and.drink | 기내식 만족도 | 순서형 (0 ~ 5) |
| Gate.location | 게이트 위치 만족도 | 순서형 (0 ~ 5) |
| Inflight.entertainment | 기내 오락 만족도 | 순서형 (0 ~ 5) |
| Online.support | 온라인 지원 만족도 | 순서형 (0 ~ 5) |
| Ease.of.Online.booking | 온라인 예약 용이성 | 순서형 (0 ~ 5) |
| On.board.service | 기내 서비스 만족도 | 순서형 (0 ~ 5) |
| Leg.room.service | 레그룸 만족도 | 순서형 (0 ~ 5) |
| Baggage.handling | 수화물 처리 만족도 | 순서형 (0 ~ 5) |
| Checkin.service | 체크인 서비스 만족도 | 순서형 (0 ~ 5) |
| Cleanliness | 청결도 | 순서형 (0 ~ 5) |
| Online.boarding | 온라인 탑승 만족도 | 순서형 (0 ~ 5) |
| Departure.Delay | 출발 지연 시간(분) | 수치형 |
| Arrival.Delay | 도착 지연 시간(분) | 수치형 |

<br/>


### 분석을 수행하기 전 예상하는 결과

- 고객의 성별, 고객의 연령, 고객의 탑승 목적, 비행 거리별 항공사의 서비스 만족에 대한 오즈의 차이는 없거나, 작을 것이다.
- 충성도가 높은 고객이, 그렇지 않은 고객보다 항공사의 서비스에 만족하는 경향이 있을 것이다.
- ‘이코노미 -> 이코노미 플러스 -> 비즈니스’의 좌석 유형 순으로 항공사의 서비스 만족에 대한 오즈가 높을 것이다.
- 모든 순서형 변수는 증가할수록, 항공사의 서비스 만족에 대한 오즈가 높아질 것이다.
- 도착 지연 시간과 출발 지연 시간이 증가할수록, 항공사의 서비스 만족에 대한 오즈가 낮아질 것이다.

<br/>


## 2. 본론

<br/>


### 데이터 전처리
```
data <- read.csv("US Airline Passenger Satisfaction.csv", header = T)
sum(is.na(data)) # 결측값 확인
```
```
## [1] 393
```
```javascript
data <- na.omit(data) # 결측값 제거
sum(is.na(data))
```
```
## [1] 0
```

- 분석에 앞서, 데이터內 393개의 결측값의 존재를 확인, 본 프로젝트는 결측값을 별도의 값으로 대체하지 않고, 제외하여 분석하는 완전분석법에 따라 진행하기로 결정.

```javascript
data$Satisfaction <- as.factor(data$Satisfaction)
data$Gender <- as.factor(data$Gender)
data$Customer.Type <- as.factor(data$Customer.Type)
data$Class <- as.factor(data$Class)
data$Type.of.Travel <- as.factor(data$Type.of.Travel)
dim(data)
```
```
## [1] 129487     22
```
- as.factor()함수를 통해 명목형 변수들을 설정.
- 모형의 간결함과, 해석의 용이성을 위하여, 본 분석에서는 순서형 변수들을 양적변수로 간주하여 모형을 적합하기로 결정.
- 전처리를 마무리한 데이터의 차원은 129487개의 행과, 22개의 열로 구성됨을 확인.

<br/>


### 적합할 모형 설정
- 모형 설정에 앞서 이 데이터는 총 21개의 많은 설명변수들을 포함하는 만큼, 양적변수들간의 상관계수 및 VIF값을 파악하여 높은 상관관계 여부를 파악할 필요가 있다.
```javascript
quan_var <- cbind(data[,4], data[,7:22]) ; colnames(quan_var) <- c(3, 6:21)
cor <- cor(quan_var) 
tail(round(cor,3), 5)
```
```
##         3     6      7     8      9     10     11     12     13     14    15     16     17     18     19     20     21
## 17  0.032 0.003  0.041 0.063  0.012 -0.032  0.224  0.207  0.138  0.251 0.168  0.241  1.000  0.242  0.184 -0.021 -0.024
## 18 -0.019 0.009  0.109 0.067  0.033 -0.002  0.109  0.096  0.418  0.551 0.412  0.632  0.242  1.000  0.106 -0.062 -0.067
## 19  0.038 0.010  0.130 0.000  0.014 -0.003  0.356  0.670  0.684  0.140 0.113  0.112  0.184  0.106  1.000 -0.020 -0.022
## 20 -0.009 0.112 -0.024 0.004 -0.013  0.004 -0.030 -0.034 -0.037 -0.038 0.004 -0.010 -0.021 -0.062 -0.020  1.000  0.965
## 21 -0.011 0.110 -0.026 0.003 -0.015  0.004 -0.033 -0.036 -0.040 -0.041 0.000 -0.014 -0.024 -0.067 -0.022  0.965  1.000
```
```javascript
GGally::ggcorr(quan_var, name = "corr", label = T)
```
<img width="840" height="600" src="https://user-images.githubusercontent.com/100699925/158415697-229ea9d7-f6d6-4f01-ab67-7363f30f712b.png">

<br/>

```javascript
diag(solve(cor))
```
```
##         3         6         7         8         9        10        11        12 
##  1.114663  1.087554  2.380131  1.675573  2.730450  1.700751  1.787804  2.299574 
##        13        14        15        16        17        18        19        20 
##  3.320140  1.764527  1.373716  1.917640  1.209643  2.028954  2.565907 14.672077 
##        21 
## 14.677267
```

<br/>

- 상관계수 행렬 cor과 이를 “GGally”패키지의 ggcorr()함수를 통해 히트맵으로 시각화한 결과, 20번째 설명변수(출발 지연 시간)와, 21번째 설명변수(도착 지연 시간)간 높은 상관관계가 있음을 확인 할 수 있었다. 또한 해당 변수들의 VIF값들 또한 10이상의 값을 가짐으로서, 다중공선성의 존재를 알 수 있다.
- 이는 상식적으로 항공기의 출발에 지연이 있다면, 도착에 지연이 발생할 확률이 높아지는 것은 당연한 결과라고 할 수 있다. 따라서 이에 대한 보완으로, 두 변수중 하나인 ‘출발 지연 시간’을 분석에서 제외하기로 결정하였다.
- 이후 다시 계산한 VIF값에서는 10이상의 값을 가지는 변수가 없음을 확인하였다.

```javascript
set.seed(2021)
idx <- sample(1:nrow(data), 10000)
dat <- data[idx,]
dim(dat)
```
```
## [1] 10000    22
```

<br/>

- 129487명의 고객 데이터에서, 10000명의 랜덤 추출한 데이터를 사용 

```javascript
Fullmod = glm(Satisfaction ~ ., dat = dat[,-21], family = binomial(link = "logit"))
backward <- step(Fullmod, direction = "backward") ; formula(backward)
```
- ‘출발 지연 시간’을 제외한 모든 설명변수를 포함한 완전모형에서 step()함수의 후진 소거법을 적용하여 최저의 AIC값을 가지는 모형을 탐색한 결과, 완전모형과 동일한 모형을 얻을 수 있었다.
- 이에 따라 본 분석에서는 완전모형을 적합하기로 결정하였다.

<br/>


### 모형 적합
```javascript
summary(Fullmod)
```
```
## 
## Call:
## glm(formula = Satisfaction ~ ., family = binomial(link = "logit"), 
##     data = dat[, -21])
## 
## Deviance Residuals: 
##     Min       1Q   Median       3Q      Max  
## -2.8028  -0.5798   0.2216   0.5267   3.6043  
## 
## Coefficients:
##                                 Estimate Std. Error z value Pr(>|z|)    
## (Intercept)                   -6.829e+00  2.277e-01 -29.991  < 2e-16 ***
## GenderMale                    -9.560e-01  5.853e-02 -16.332  < 2e-16 ***
## Customer.TypeLoyal Customer    2.003e+00  8.900e-02  22.507  < 2e-16 ***
## Age                           -9.378e-03  2.051e-03  -4.572 4.83e-06 ***
## Type.of.TravelPersonal Travel -7.245e-01  8.332e-02  -8.695  < 2e-16 ***
## ClassEco                      -7.364e-01  7.531e-02  -9.779  < 2e-16 ***
## ClassEco Plus                 -6.683e-01  1.180e-01  -5.662 1.49e-08 ***
## Flight.Distance               -1.105e-04  3.044e-05  -3.631 0.000283 ***
## Seat.comfort                   3.054e-01  3.223e-02   9.474  < 2e-16 ***
## Dep.Arr.time.convenient       -2.448e-01  2.471e-02  -9.904  < 2e-16 ***
## Food.and.drink                -1.514e-01  3.313e-02  -4.569 4.91e-06 ***
## Gate.location                  1.427e-01  2.775e-02   5.142 2.71e-07 ***
## Inflight.entertainment         6.421e-01  2.905e-02  22.103  < 2e-16 ***
## Online.support                 5.380e-02  3.227e-02   1.667 0.095556 .  
## Ease.of.Online.booking         2.061e-01  3.852e-02   5.350 8.80e-08 ***
## On.board.service               3.228e-01  3.010e-02  10.723  < 2e-16 ***
## Leg.room.service               2.132e-01  2.481e-02   8.594  < 2e-16 ***
## Baggage.handling               1.192e-01  3.248e-02   3.671 0.000242 ***
## Checkin.service                3.043e-01  2.466e-02  12.343  < 2e-16 ***
## Cleanliness                    5.587e-02  3.425e-02   1.631 0.102837    
## Online.boarding                1.565e-01  3.389e-02   4.617 3.89e-06 ***
## Arrival.Delay                 -4.410e-03  8.100e-04  -5.445 5.17e-08 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## (Dispersion parameter for binomial family taken to be 1)
## 
##     Null deviance: 13739.5  on 9999  degrees of freedom
## Residual deviance:  7822.3  on 9978  degrees of freedom
## AIC: 7866.3
## 
## Number of Fisher Scoring iterations: 5
```


- 모든 설명변수를 포함한 완전모형을 적합하였을 때의 회귀계수, 이탈도, AIC값은 위의 결과와 같다.
- 모든 회귀계수 추정값은 통계적으로 유의하며, 모형의 이탈도는 7510.5로서, 자유도 9978의 카이제곱분포 상위 0.05분위수  10211.49보다 작다. 따라서 현재모형의 적합도는 좋다고 해석 할 수 있다.

<br/>

```javascript
qchisq(0.95, 9978) 
```
```
## [1] 10211.49
```
- 다만, 데이터가 10000 X 21 의 차원을 가진 크기가 큰 데이터라는 점을 고려하였을 때, 위 결과의 낮은 P-값들과 그에 따른 통계적 유의성은 데이터의 크기 때문일 수도 있다는 점을 인지해야 된다.

<br/>


### 오즈비 계산 및 회귀 계수 해석

|	| **round(exp(Fullmod$coefficients), 4)** |
| ---- | ---- |
| (Intercept)	| 0.0011 |
| GenderMale	| 0.3844 |
| Customer.TypeLoyal Customer	| 7.4125 |
| Age	| 0.9907 |
| Type.of.TravelPersonal | Travel	0.4846 |
| ClassEco | 0.4788 |
| ClassEco Plus | 0.5126 |
| Flight.Distance | 0.9999 |
| Seat.comfort | 1.3571 |
| Dep.Arr.time.convenient | 0.7829 |
| Food.and.drink | 0.8595 |
| Gate.location | 1.1534 |
| Inflight.entertainment | 1.9005 |
| Online.support | 1.0553 |
| Ease.of.Online.booking | 1.2289 |
| On.board.service | 1.3810 |
| Leg.room.service | 1.2377 |
| Baggage.handling | 1.1266 |
| Checkin.service | 1.3557 |
| Cleanliness | 1.0575 |
| Online.boarding | 1.1694 |
| Arrival.Delay | 0.9956 |

- 고객의 성별이 남성일 때의 오즈가, 여성일 때의 오즈의 0.39배이다. 즉, 서비스에 만족하는 경향은 남성보다 여성이 더 크다.
- 충성도가 높은 고객의 오즈가 충성도가 낮은 고객의 오즈보다 7.5배 크다.
- 고객의 연령이 1세 증가할 때마다, 서비스 만족에 대한 오즈는 1%씩 감소한다.
- 개인적 목적의 이용고객의 오즈가 비즈니스 목적 이용고객의 오즈의 0.47배이며, 서비스에 만족하는 경향은 비즈니스 목적 이용고객이 더 크다.
- 좌석 유형이 이코노미와 이코노미 플러스 일 때의 오즈는 각각 비즈니스 석 일 때 오즈의 0.45, 0.42배이다. 즉 ‘이코노미 플러스 -> 이코노미 -> 비즈니스’의 순으로 항공사의 서비스 만족에 대한 오즈가 높다.
- 비행거리가 1노트 증가할 때마다, 서비스 만족에 대한 오즈는 0.1%씩 감소한다.
- 도착/출발 편리성과, 기내식 만족도를 제외한 모든 만족도는 점수가 1점 증가할 때마다, 서비스 만족에 대한 오즈가 증가한다.
- 도착 지연 시간이 1분 증가할 때마다, 서비스 만족에 대한 오즈는 0.6%씩 감소한다.

<br/>


### 모형의 예측값은, 전체 데이터에 대하여 얼마나 높은 정확도를 보이는가?

- 분석에서 적합한 로지스틱 모형의 평가를 위하여, 모형을 바탕으로 각 고객이 항공사의 서비스에 만족할 확률()을 예측하여 실제 고객의 응답결과와 대조하면 예측 정확도, 즉 ‘정분류율’을 계산할 수 있다.

- (단 pred()함수의 type을 “response”로 설정하여 계산된 각각의 예측값()들을 반올림하여, 0.5이상이라면 1(만족), 그렇지 않다면 0이라는 가정이 필요하다.)

```javascript
pred <- predict(Fullmod, newdata = data, type = "response")
pred1 <- as.factor(ifelse(pred < 0.5, 0, 1))
(CM <- table(pred1, data$Satisfaction)) 
```
```
##      
## pred1     0     1
##     0 47602 10347
##     1 11003 60535
```
```javascript
mean(pred1 == data$Satisfaction)
```
```
## [1] 0.8351186
```
- 위의 결과의 CM은 예측값들과 실제값들을 대조한 행렬이다. (가로 = 실제값, 세로 = 예측값)
- 분석에서 적합한 모형을 바탕으로 예측한 고객들의 만족여부가, 실제 고객들의 만족여부와 일치할 확률은 0.8357 (83.57%)임을 알 수 있다.

<br/>


## 3. 결론

<br/>


### 실제 분석결과와 예상했던 결과와의 비교

- 예상과 다르게 고객의 성별별로 만족에 대한 오즈의 차이가 있으며, 여성이 남성보다 서비스에 만족하는 경향이 크다.
- 예상과 동일하게 충성도가 높은 고객이 충성도가 낮은 고객보다 서비스에 만족하는 경향이 크다
- 예상과 동일하게 고객의 연령이 증가할 때마다 서비스에 만족하는 경향은 감소하지만, 그 폭은 크지 않다.
- 예상과 다르게 고객의 이용목적별로 만족에 대한 오즈의 차이가 있으며, 비즈니스 목적 이용고객이 개인적 목적 이용고객보다 서비스에 만족하는 경향이 크다.
- 예상과 비슷하게 좌석 유형별 만족에 대한 오즈의 차이가 있으나, ‘이코노미 -> 이코노미 플러스 -> 비즈니스’의 순으로 서비스에 만족하는 경향이 크다.
- 예상과 동일하게 비행거리가 증가할 때마다 서비스에 만족하는 경향은 감소하지만, 그 폭은 크지 않다.
- 예상과 다르게 모든 순서형 변수가 값이 증가할수록, 서비스 만족에 대한 경향이 커지지는 않는다.
- 예상과 다르게 도착 지연 시간이 증가할수록 서비스 만족에 대한 경향은 감소하지만, 그 폭은 크지 않다.

<br/>


### 가능한 추가 분석의 방향

- 다른 국가 및 항공사 데이터의 수집과 분석을 통한, 본 프로젝트와의 비교.
- 2015년만이 아닌 최근 데이터의 수집과 분석을 통한, 본 프로젝트와의 비교.
- 전체 데이터를 사용하는 것이 아닌, 훈련데이터/평가데이터의 분할 및 추출을 통한 교차 검증(Cross Validation)과 그에 따른 예측 정확도 개선.
- 이외의 예측·분류 알고리즘(예 : 서포트 벡터 머신, 부스팅, 의사결정나무 등)들을 적용하여 로지스틱 회귀와 비교. 
