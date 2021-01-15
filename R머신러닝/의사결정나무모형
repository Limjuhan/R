
library(tidyverse)
url <- 'https://bit.ly/universal_bank'
guess_encoding(file = url)
df <- read.csv(file = url)
str(object = df)
summary(object = df)
head(x = df, n = 10L)

class(x = 10)
class(x = 10L)

df <- df %>% select(-ID, -ZIP.Code) %>% filter(Experience >= 0)

cols <- c(6, 8:12)
df[, cols] <- map_df(.x = df[, cols], .f = as.factor)
summary(object = df)

df$PersonalLoan %>% table() %>% prop.table() %>% round(digits = 4L) * 100

n <- nrow(x = df)
set.seed(seed = 1234)
index <- sample(x = n, size = n * 0.7, replace = FALSE)
trainSet <- df %>% slice(index)
testSet <- df %>% slice(-index)

trainSet$PersonalLoan %>% table() %>% prop.table() %>% round(digits = 4L) * 100
testSet$PersonalLoan %>% table() %>% prop.table() %>% round(digits = 4L) * 100

getwd()
setwd(dir = './data')
# save.image(file = 'Bank_Dataset.RDA')
# load(file = 'Bank_Dataset.RDA')

save(list = c('df', 'trainSet', 'testSet'), 
     file = 'Bank_Dataset.RDA')
load(file = 'Bank_Dataset.RDA')

library(rpart)
ctrl <- rpart.control(minsplit = 20, cp = 0.01, maxdepth = 10)
set.seed(seed = 1234)
fit1 <- rpart(formula = PersonalLoan ~ .,
              data = trainSet, 
              control = ctrl)
summary(object = fit1)

library(rpart.plot)
rpart.plot(x = fit1,
           type = 0,
           extra = 106,
           fallen.leaves = TRUE)

printcp(x = fit1)
plotcp(x = fit1)


ctrl <- rpart.control(minsplit = 10, cp = 0.001, maxdepth = 30)
set.seed(seed = 1234)
fit1 <- rpart(formula = PersonalLoan ~ .,
              data = trainSet, 
              control = ctrl)
printcp(x = fit1)
plotcp(x = fit1)

fit2 <- prune.rpart(tree = fit1, cp = 0.0098)
printcp(x = fit2)
plotcp(x = fit2)

rpart.plot(x = fit1,
           type = 2,
           extra = 101,
           fallen.leaves = FALSE)

rpart.plot(x = fit2,
           type = 2,
           extra = 101,
           fallen.leaves = FALSE)

real <- testSet$PersonalLoan
pred1 <- predict(object = fit1, newdata = testSet, type = 'class')
pred2 <- predict(object = fit2, newdata = testSet, type = 'class')

table(pred1, real)
table(pred2, real)

library(caret)
confusionMatrix(data = pred1, reference = real, positive = '1')
confusionMatrix(data = pred2, reference = real, positive = '1')

library(MLmetrics)
F1_Score(y_true = real, y_pred = pred1, positive = '1')
F1_Score(y_true = real, y_pred = pred2, positive = '1')

prob1 <- predict(object = fit1, newdata = testSet, type = 'prob')[, 2]
prob2 <- predict(object = fit2, newdata = testSet, type = 'prob')[, 2]

library(pROC)
roc(response = real, predictor = prob1) %>% 
  plot(main = 'ROC Curve', col = 'red', lty = 1)

roc(response = real, predictor = prob2) %>% 
  plot(col = 'blue', lty = 2, add = TRUE)

auc(response = real, predictor = prob1)
auc(response = real, predictor = prob2)

getwd()
saveRDS(object = fit2, file = 'DecisionTree.RDS')
