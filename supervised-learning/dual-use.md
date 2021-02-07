# Dual Use

1. Neural Networks
2. Support Vector Machines



### 1. Neural Networks

{% file src="../.gitbook/assets/ann.pptx" caption="ANN with Neural Networks" %}

{% code title="Artificial Neural Network" %}
```r
# Importing the dataset
#dataset = read.csv('Churn_Modelling.csv')
dataset= read.csv(file.choose())
dataset = dataset[4:14]
str(dataset)

# Encoding the categorical variables as factors
dataset$Geography = as.numeric(factor(dataset$Geography,
                                      levels = c('France', 'Spain', 'Germany'),
                                      labels = c(1, 2, 3)))
dataset$Gender = as.numeric(factor(dataset$Gender,
                                   levels = c('Female', 'Male'),
                                   labels = c(1, 2)))

# Splitting the dataset into the Training set and Test set
# install.packages('caTools')
library(caTools)
set.seed(123)
split = sample.split(dataset$Exited, SplitRatio = 0.8)
training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)

# Feature Scaling
training_set[-11] = scale(training_set[-11])
test_set[-11] = scale(test_set[-11])

# Fitting ANN to the Training set
install.packages('h2o')
library(h2o)
h2o.init(nthreads = -1)
h2o.init(nthreads = -1, max_mem_size = '2g', ip = "127.0.0.1", port = 54321)
h2o.init(nthreads = -1, max_mem_size = '2g', ip = "127.0.0.1", port = 50001)

model = h2o.deeplearning(y = 'Exited',
                         training_frame = as.h2o(training_set),
                         activation = 'Rectifier',
                         hidden = c(5,5),
                         epochs = 100,
                         train_samples_per_iteration = -2)

# Predicting the Test set results
y_pred = h2o.predict(model, newdata = as.h2o(test_set[-11]))
y_pred = (y_pred > 0.5)
y_pred = as.vector(y_pred)

# Making the Confusion Matrix
cm = table(test_set[, 11], y_pred)

# h2o.shutdown()
```
{% endcode %}



### 2. Support Vector Machines

{% code title="Support Vector Machine \(SVM\) " %}
```r
# Importing the dataset
#dataset = read.csv('Social_Network_Ads.csv')
dataset = read.csv(file.choose())

dataset = dataset[3:5]
str(dataset)

# Encoding the target feature as factor
dataset$Purchased = factor(dataset$Purchased,
                           levels = c(0, 1))
str(dataset)

# Splitting the dataset into the Training set and Test set
# install.packages('caTools')
library(caTools)
set.seed(123)

split = sample.split(dataset$Purchased, SplitRatio = 0.75)

training_set = subset(dataset, split == TRUE)
test_set = subset(dataset, split == FALSE)

# Feature Scaling
training_set[-3] = scale(training_set[-3])
test_set[-3] = scale(test_set[-3])

# Fitting SVM to the Training set
install.packages('e1071')
library(e1071)

classifier = svm(formula = Purchased ~ .,
                 data = training_set,
                 type = 'C-classification',
                 kernel = 'radial')

# Predicting the Test set results
y_pred = predict(classifier, newdata = test_set[-3])

# Making the Confusion Matrix
cm = table(test_set[, 3], y_pred)
cm
```
{% endcode %}



