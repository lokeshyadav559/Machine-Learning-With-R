# Numeric Prediction

1. Forecasting Numeric Data
2. Simple Linear Regression
3. Polynomial Regression
4. Ordinary least squares estimation
5. Correlations
6. Multiple Linear Regression



### 1. Forecasting Numeric Data

```r
# Importing the dataset
dataset = read.csv(file.choose())
View(dataset)

# Taking care of missing data
dataset$Age
dataset$Age = ifelse(is.na(dataset$Age),
                     ave(dataset$Age,
                     FUN = function(x) mean(x, na.rm = TRUE)),
                     dataset$Age)
dataset$Salary = ifelse(is.na(dataset$Salary),
                        ave(dataset$Salary,
                        FUN = function(x) mean(x, na.rm = TRUE)),
                        dataset$Salary)
View(dataset)

# Encoding categorical data
dataset$Country = factor(dataset$Country,
                         levels = c('France', 'Spain', 'Germany'),
                         labels = c(1, 2, 3))
str(dataset)
dataset$Purchased = factor(dataset$Purchased,
                           levels = c('No', 'Yes'),
                           labels = c(0, 1))

# SPlitting the Dataset into 2 parts
##1.training
##2.testing

install.packages('caTools')
library(caTools)
set.seed(123)

##0.8 as 80% for training and 20% for testing
split = sample.split(dataset$Purchased, SplitRatio = 0.8)
split

##Purchased because we need to put the last column of dataset
training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)

training_set = scale(training_set)
test_set = scale(test_set)

#Feature Scaling 
training_set[,2:3]
training_set[,2:3] = scale(training_set[,2:3])
test_set[,2:3] = scale(test_set[,2:3])

View(test_set)
View(training_set)
```



### 2. Simple Linear Regression

Linear regression is used to predict the value of an outcome variable _Y_  based on one or more input predictor variables _X_. The aim is to establish a linear       relationship \(a mathematical formula\) between the predictor variable\(s\) and the response variable, so that, we can use this formula to estimate the value of the response _Y_, when only the predictors \(_X_\) values are known.

Such as predicting a value, e.g. house prices

{% file src="../.gitbook/assets/linear-regression.pptx" caption="Linear Regression" %}



{% code title="Simple Linear Regression" %}
```r
# Importing the dataset
dataset = read.csv(file.choose())
View(dataset)

# Splitting the dataset into the Training set and Test set
install.packages('caTools')
library(caTools)
set.seed(123)

split = sample.split(dataset$Salary, SplitRatio = 2/3)
split

training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)

View(training_set)
View(test_set)

# Feature Scaling
# training_set = scale(training_set)
# test_set = scale(test_set)

# Fitting Simple Linear Regression to the Training set
regressor = lm(formula = Salary ~ YearsExperience,
               data = training_set)

summary(regressor)

# Predicting the Test set results
y_pred = predict(regressor, newdata = test_set)
y_pred

# Visualising the Training set
install.packages("ggplot2")
library(ggplot2)
ggplot() +
  geom_point(aes(x = training_set$YearsExperience,
                 y = training_set$Salary),
             colour = 'red') +
  geom_line(aes(x = training_set$YearsExperience,
                y = predict(regressor, newdata = training_set)),
            colour = 'blue') +
  ggtitle('Salary vs Experience (Training set)') +
  xlab('Years of experience') +
  ylab('Salary')

# Visualising the Test set results
library(ggplot2)
ggplot() +
  geom_point(aes(x = test_set$YearsExperience,
                 y = test_set$Salary),
             colour = 'red') +
  geom_line(aes(x = training_set$YearsExperience,
                y = predict(regressor, newdata = training_set)),
            colour = 'blue') +
  ggtitle('Salary vs Experience (Test set)') +
  xlab('Years of experience') +
  ylab('Salary')
```
{% endcode %}



### 3. Polynomial Regression

A polynomial regression differs from the ordinary linear regression because it adds terms that allow the regression line or plane to curve.

{% code title="Polynomial Regression" %}
```r
# Importing the dataset
dataset = read.csv(file.choose())
dataset = dataset[,2:3]

# Splitting the dataset into the Training set and Test set
# # install.packages('caTools')
# library(caTools)
# set.seed(123)
# split = sample.split(dataset$Salary, SplitRatio = 2/3)
# training_set = subset(dataset, split == TRUE)
# test_set = subset(dataset, split == FALSE)

# Feature Scaling
# training_set = scale(training_set)
# test_set = scale(test_set)

# Fitting Linear Regression to the dataset
lin_reg = lm(formula = Salary ~ .,
             data = dataset)
summary(lin_reg)

# Fitting Polynomial Regression to the dataset
dataset$Level2 = dataset$Level^2
dataset$Level3 = dataset$Level^3
View(dataset)

#dataset$Level4 = dataset$Level^4
poly_reg = lm(formula = Salary ~ .,
              data = dataset)

# Visualising the Linear Regression results
# install.packages('ggplot2')
library(ggplot2)
ggplot() +
  geom_point(aes(x = dataset$Level, y = dataset$Salary),
             colour = 'red') +
  geom_line(aes(x = dataset$Level, y = predict(lin_reg, newdata = dataset)),
            colour = 'blue') +
  ggtitle('Truth or Bluff (Linear Regression)') +
  xlab('Level') +
  ylab('Salary')

# Visualising the Polynomial Regression results
# install.packages('ggplot2')
library(ggplot2)
ggplot() +
  geom_point(aes(x = dataset$Level, y = dataset$Salary),
             colour = 'red') +
  geom_line(aes(x = dataset$Level, y = predict(poly_reg, newdata = dataset)),
            colour = 'blue') +
  ggtitle('Truth or Bluff (Polynomial Regression)') +
  xlab('Level') +
  ylab('Salary')

# Predicting a new result with Linear Regression
y1=predict(lin_reg, data.frame(Level = 6.5))
y1
# Predicting a new result with Polynomial Regression
y2=predict(poly_reg, data.frame(Level = 6.5,
                                Level2 = 6.5^2,
                                Level3 = 6.5^3,
                                Level4 = 6.5^4))
y2
```
{% endcode %}



### 4. Ordinary Least Squares Estimation

Ordinary Least Squares \(OLS\) linear regression is a statistical technique used for the analysis and modelling of linear relationships between a response variable and one or more predictor variables.

```r
dataset=read.csv(file.choose())
dataset
View(dataset)
head(dataset)

m<-cor(dataset[3:5])
m

corrplot(m,method="number")

results<-lm(lc~Mileage+lh,dataset)
results

#lc=b0+b1lh
#lc=1.375-0.0000847mileage+73.55lh

summary(results)
resultz<-lm(lc~lh,dataset)
resultz

#lc=-0.236+73.5088lh###final model
predict(resultz,data.frame(lh=10))

#predicting the results for 95%confidence (default)
predict(resultz,data.frame(lh=10),interval ='confidence' )
#a prediction interval is an estimate of an interval in 
#which a future observation will fall
```



### 5. Correlations



```r
cars()
data()

cars 
d<-carsd
cor(d)
cr<-cor(d)
cr
install.packages('corrplot')
corrplot(cr,method="pie")

corrplot(cr,method="color")

corrplot(cr,method="number")

corrplot(cr,type="lower")

corrplot(cr,type="upper")

mtcars
e<-mtcars
e
cor(e)
t<-cor(e)
t
corrplot(t,method="number")
```



### 6. Multiple Linear Regression



{% code title="Multiple Linear Regression" %}
```r
# Importing the dataset
dataset = read.csv('50_Startups.csv')
dataset=read.csv(file.choose())
View(dataset)

str(dataset)

# Encoding categorical data
dataset$State = factor(dataset$State,
                       levels = c('New York', 'California',
                                  'Florida'),
                       labels = c(1, 2, 3))

# Splitting the dataset into the Training set and Test set
# install.packages('caTools')
library(caTools)
set.seed(123)

split = sample.split(dataset$Profit, SplitRatio = 0.8)

training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)
View(training_set)
View(test_set)
nrow(training_set)

# Feature (Scaling)
# training_set = scale(training_set)
# test_set = scale(test_set)

# Fitting Multiple Linear Regression to the Training set
regressor = lm(formula = Profit ~ .,
               data = training_set)
summary(regressor)

# Predicting the Test set results
y_pred = predict(regressor, newdata = test_set)
y_pred

# Building the optimal model using Backward Elimination

regressor = lm(formula = Profit ~ R.D.Spend +
                 Administration + Marketing.Spend + State,
               data = dataset)
summary(regressor)

regressor = lm(formula = Profit ~ R.D.Spend +
                 Administration + Marketing.Spend,
               data = dataset)
summary(regressor)

regressor = lm(formula = Profit ~ R.D.Spend + Marketing.Spend,
               data = dataset)
summary(regressor)

regressor = lm(formula = Profit ~ R.D.Spend,
               data = dataset)
summary(regressor)
```
{% endcode %}



