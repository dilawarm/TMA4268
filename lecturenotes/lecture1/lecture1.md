# Classification
---
## Discriminant analysis
* Discriminant analysis is relying on _samplig diagram_.
* The appproach is to model the distribution of $X$ in ech of the clases separately, and then use Bayes therorem to flip things around and obtain $Pr(Y\lvert X)$
* The decision booundary is where the point of intersection of the two lines is, because here
$$\pi_1f_1(x)=\pi_2f_2(x).$$
* For different priors $\pi_1=0.3$ and $\pi_2=0.7$, the decision boundary shifts to the left.
## WHy discriminant analysis?
* Linear discriminant analysis is more stable than logstis regresenion when
  * the clases are well-separated.
  * $n$ is sall and the distribution of the predcitors $X$ is approximately normal in each of the classes.
* Moreover, linear discrimiant analyss is popular when we have more than two respone classes.
## LDA when $p=1$
* Class condiationa distribtuibs $f_x(X)$ are assymed normal for $k=1,\dots,K$ that is

$$f_k(x)=\frac{1}{\sqrt{2\pi}\sigma_k}e^{-\frac{1}{2}\left(\frac{x-\mu_k}{\sigma_k}\right)^2}$$

* We can insert rhe expression for each class distribtuib into Bayes formula to obtain the posterior probability $p_k(x)=Pr(Y=k\lvert X=x)$

* Our rule is to classify to the class for which $p_k(x)$ is largest.

$$\begin{aligned}
S_k(x)&=\log(p_k(x))\\
&=\log(\pi_k)+\frac{1}{2}\frac{2\mu_kx}{\sigma^2}-\frac{\mu_k^2}{2\sigma^2}\\
&=x\frac{\mu_k}{\sigma^2}-\frac{\mu_k^2}{2\sigma^2}+\log(\pi_k)+constant
\end{aligned}$$
For example: $K=2, \pi_1=\pi_2:$

### Decision boundary:
$$S_1(x)=S_2(x)$$
$$x\frac{\mu_k}{\sigma^2}-\frac{\mu_k^2}{2\sigma^2}+\log(0.5)=x\frac{\mu_k}{\sigma^2}-\frac{\mu_k^2}{2\sigma^2}+\log(0.5)$$
$$\implies x(\mu_1-\mu_2)=\frac{\mu_1^2}{2}-\frac{\mu_2^2}{2}$$
$$\implies x=\frac{\mu_1+\mu_2}{2}$$

* The Bayes error rate: `round(pnorm(0.2,1.5))=0.09.`

* Typically we don't know the distributions

## Parameter estimators
* Prior:
$$\hat{\pi_k}=\frac{n_k}{n}$$
* Mean:
$$\hat{\mu_k}=\frac{1}{n_k}\sum_{i:y_i=k}x_i$$
* Standard deviation
$$\hat{\sigma^2}=\frac{1}{n-K}\sum_{k=1}^J{\sum_{i:y_i=k}}{(x_i-\hat{\mu_k})^2}=\sum_{k=1}^K{\frac{n_k-1}{n-K}}\cdot\hat{\sigma_k}^2$$
## How to test the goodness of the estimator?
1. Use the training set to estimate parameters and class bondary.
2. Use the test set to estimate misclassification rate.

```{R}
train1 = rnorm(n1train, mu1, sigma)
test2 = rnorm(n2test, mu2, sigma)
```
Then set
$$\hat{\delta_1}(x)=\hat{\delta_2}(x)$$
and resolve for $x$ to obtain a decison rule (boundary).

$$S_1(x)=S_2(x)$$
$$x\frac{\hat{\mu_k}}{\hat{\sigma_1}}-\frac{\hat{\mu_k}^2}{2\hat{\sigma_1}}+\log(0.5)=x\frac{\hat{\mu_k}}{\hat{\sigma_2}}-\frac{\hat{\mu_k}^2}{2\hat{\sigma_2}}+\log(0.5)$$

$$\implies x=\frac{\mu_1+\mu_2}{2}+\hat{\sigma}\frac{\log(\pi_2)-\log(\pi_1)}{(\mu_1-\mu_2)}$$


```{r}
trainingError <- (sum(train1 > rule) + sum(train2 < rule))/n
testError <- (sum(test1 > rule) + sum(test < rule))/n
```
## The confusion matrix
* THe confusion matrix is a table that can show the performance of a classifier, given that the values are known.
* We can make a confusion matrix from the training or test set.
* The sum of the diagonal is the total number of correct classifications. The sum of all elements of the diangonal is the total number of misclassifications.

## Multivariate LDA $(p>1)$
* LDA can be generalied to situations when $p>1$ covariates are used. The decision boundaries are still linear
* Plugging the density function into equation $3$ gives the following expression for the discriminat function:

$$\delta_k(x)=x^T\Sigma^{-1}\mu_k-\frac{1}{2}\mu_k^T\Sigma^{-1}\mu_k+\log\pi_k$$

* Boundary is given at

$$\delta_A(x)=\delta_B(x)$$

* From the example we get a boundary with functional form

$$x_2=4-x_1$$

```{r}
r.pred <- ifelse(df$X2 < 4 - df$X1, "A", "B")
table(real = df$class, r.pred)
```

* We also have to estimate here:

* Prior:
$$\hat{\pi_k}=\frac{n_k}{n}$$
* Mean:
$$\hat{\mu_k}=\frac{1}{n_k}\sum_{i:y_i=k}X_i$$
* Standard deviation
$$\hat{\Sigma}=\frac{1}{n-K}\sum_{k=1}^J{\sum_{i:y_i=k}}{(x_i-\hat{\mu_k})^T}=\sum_{k=1}^K{\frac{n_k-1}{n-K}}\cdot\hat{\Sigma_k}$$

```{r}
r.lda <- lda(class ~ X1 + X2, df)
```
## Posterior probabilities
* Sometimes the probability that an obsrvation comes from a class $k$ is more interesting than the actual classification itself.
* These class probabilities can be estimated from the priors and class condiationa distributions, or from the discriminant functions.
## QDA
* In LDA we assumed that
$$\Sigma_k=\Sigma$$
for all classes.
* In QDA we allow different covariance matrices $\Sigma_k$ for each class, while the predictors are still multivariate Gaussian.
$$X\sim\mathcal{N}(\mu_k,\Sigma_k).$$
* The decision boundaries are _quadratic_ functions of $x.$
## LDA vs QDA
* QDA is more flexible than LDA, as it allows fro group-specific covariance matrices.
* Increase chance to overfit (high variance) because of more flexibility

## Different forms of discriminant analysis
* LDA
* QDA
* Naive Bayes: Assyme that each class density is the product of maringal densitirs - i.e. inputs are conditionally independent in each class

$$f_k(x)=\prod_{j=1}^p{f_{kj}(x_j)}$$
This is generally not true, but it simplifies the estimation dramatically.
* Other forms by proposinf specific dentiry models for $f_k(x),$ including nonparametric approaches.
## Naive Bayes
* Naive Bayes is method that is popular when $p$ is large.
## Summary of Classification Methods
* Logistic regression
* KNN
* LDA
* QDA
* Naive Bayes

Remember:
* Logistic regression and KNN _directly estimate_ $Pr(Y=k\lvert X=x)$ (diagnostic paradigm).
* LDA, QDA and naive Bayes _indirectly estimate_ $Pr(Y=k\lvert X=x)\propto f_k(x)\cdot\pi_k$ (sampling paradigm).

## Which classification method is the best?
### Advantages of discriminant analysis
* Discriminant analysis is more stable than logistic regression when the classes are well-separated.
* Discriminant analysis is more stable than logistic regression of the number of observations $n$ is small and the distribution of the predictors $X$ is approximately (multivariate) normal.

## Linearity
Assume a binary classification problem with one covariate
* Recall that logistic regression can be written:

$$\log\left(\frac{p(x)}{1-p(x)}\right)=\beta_0+\beta_1x.$$
* For a two-class problem, one can show that for LDA

$$\log\left(\frac{p_1(x)}{1-p_1(x)}\right)=\log\left(\frac{p_1(x)}{p_2(x)}\right)=c_0+c_1x,$$
thus the same linear form. The difference is in how the parameters are estimated.
## LDA vs logistic regression
* Logistic regression makes no assumptions about the covariates and is therefore to be preferred in many practical applications.
* In medicine for two-class problems logistic regression is often preferred (for interpretability) and (always) together with ROC and AUC (for model comparison)
## and KNN?
* KNN is used when the class boundaries are non-linear.
## So: Which classification method is the best?
The answer: __it depends!__
* Logistic regression when $K=2$
* LDA when $n$ is small. Also when $K>2.$
* Naive Bayes is useful when $p$ is very large.
* KNN is completely different and makes no assumptions of decision boundary
## Two-class problems: sensitivity, specificity
* __Sensitivity__ is the proportion of correctly classifiied positive observations

$$\frac{TP}{P}$$
* __Specificity__ is the proportion of correctly classified negative observations
$$\frac{TN}{N}$$

* We would like that a classification rule (or a diagnostic test) have both high sensitivity and a high specificity.
* However, in an imperfect test (ie., an imperfect predictor) one usually comes atht e cost of the other.

* Can use a ROC curve for modelling the change of threshold, and checking sensitivity vs specificity.
* We want the ROC curve to "hug" the upper left corner, which is equibalent of the sensitivity and the specificity is $1$.
* AUC: Area under (ROC) curve. Larger is better. Should be larger than $0.5$