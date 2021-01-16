
library(tidyverse)
url <- 'https://bit.ly/median_house_value'
guess_encoding(file = url)
df <- read.csv(file = url)
str(object = df)
summary(object = df)

range(df$MedianHouseValue)
breaks <- seq(from = 0, to = 510000, by = 10000)
hist(x = df$MedianHouseValue, col = 'white', breaks = breaks)

df <- df %>% filter(MedianHouseValue < 500000)

n <- nrow(x = df)
set.seed(seed = 1234)
index <- sample(x = n, size = n * 0.3, replace = FALSE)
trainSet <- df %>% slice(index)
testSet <- df %>% slice(-index)

mean(x = trainSet$MedianHouseValue)
mean(x = testSet$MedianHouseValue)

save(list = c('df', 'trainSet', 'testSet'), 
     file = 'house_dataset.RDA')

library(rpart)
ctrl <- rpart.control(minsplit = 20, 
                      cp = 0.01, 
                      maxdepth = 10)
set.seed(seed = 1234)
fit1 <- rpart(formula = MedianHouseValue ~ ., 
              data = trainSet, 
              control = ctrl)
summary(object = fit1)
printcp(x = fit1)
plotcp(x = fit1)

ctrl <- rpart.control(minsplit = 10, 
                      cp = 0.001, 
                      maxdepth = 30)
set.seed(seed = 1234)
fit1 <- rpart(formula = MedianHouseValue ~ ., 
              data = trainSet, 
              control = ctrl)
printcp(x = fit1)
plotcp(x = fit1)

str(object = fit1)

# xerror만 벡터로 선택
fit1$cptable[, 4]

# 최소값의 위치 확인
which.min(x = fit1$cptable[, 4])

# cp만 벡터로 선택
cp <- fit1$cptable[76, 1]
print(x = cp)

# 가지치기
fit2 <- prune.rpart(tree = fit1, cp = cp)

real <- testSet$MedianHouseValue
pred1 <- predict(object = fit1, newdata = testSet, type = 'vector')
pred2 <- predict(object = fit2, newdata = testSet, type = 'vector')

library(MLmetrics)
MSE(y_pred = pred1, y_true = real)
RMSE(y_pred = pred1, y_true = real)
MAE(y_pred = pred1, y_true = real)
MAPE(y_pred = pred1, y_true = real)

MSE(y_pred = pred2, y_true = real)
RMSE(y_pred = pred2, y_true = real)
MAE(y_pred = pred2, y_true = real)
MAPE(y_pred = pred2, y_true = real)

# 회귀모형의 성능 평가하는 사용자 정의 함수 생성
regMeasure <- function(real, pred) {
  library(MLmetrics)
  result <- data.frame(
    MSE = MSE(y_pred = pred, y_true = real),
    RMSE = RMSE(y_pred = pred, y_true = real),
    MAE = MAE(y_pred = pred, y_true = real),
    MAPE = MAPE(y_pred = pred, y_true = real)
  )
  return(result)
}

regMeasure(real = real, pred = pred1)
regMeasure(real = real, pred = pred2)

rm(regMeasure)

getwd()
setwd(dir = '../code')
list.files()
source(file = 'myFuns.R')

regMeasure(real = real, pred = pred1)
regMeasure(real = real, pred = pred2)


# 선형 회귀모형 프로세스
# 목표변수가 정규분포를 따르는지 확인! 
# 히스토그램 그려서 보는 것도 방법!
# 목표변수와 입력변수 간 상관성 분석 : 피어슨 상관분석
# 정규분포해야 피어슨 상관분석 
# 비모수적 기법으로 상관관계를 보려면 스피어만 상관분석

test <- cor.test(x = df$MedianIncome, y = df$MedianHouseValue)
str(object = test)
test$p.value > 0.05

corrResult <- map_lgl(.x = df, .f = function(x) {
  test <- cor.test(x = x, y = df$MedianHouseValue)
  result <- test$p.value > 0.05
  return(result)
})

# 'x' 인자의 TRUE 인덱스 반환
which(x = 1:5 >= 3)
which(x = corrResult)


full <- lm(formula = MedianHouseValue ~ ., data = trainSet)
fit2 <- step(object = full, direction = 'both')
summary(object = fit2)

library(car)
vif(mod = fit2)

trainSet1 <- trainSet %>% select(-TotalBedrooms)

full <- lm(formula = MedianHouseValue ~ ., data = trainSet1)
fit2 <- step(object = full, direction = 'both')
summary(object = fit2)
vif(mod = fit2)

trainSet1 <- trainSet1 %>% select(-Households)

full <- lm(formula = MedianHouseValue ~ ., data = trainSet1)
fit2 <- step(object = full, direction = 'both')
summary(object = fit2)
vif(mod = fit2)

library(reghelper)
beta.z <- beta(model = fit2)
beta.z$coefficients[, 1]
beta.z$coefficients[, 1] %>% abs() %>% round(digits = 4L) %>% sort()

pred2 <- predict(object = fit2, newdata = testSet, type = 'response')
regMeasure(real = real, pred = pred2)
regMeasure(real = real, pred = pred1)


cols <- gray(level = seq(from = 1, to = 0, by = -0.1))
plot(x = 1:10, y = 1:10, pch = 19, col = cols, cex = 3)
