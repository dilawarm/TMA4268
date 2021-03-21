# Support Vector Machines

## Support Vector Classifiers

For some data sets a separating hyperplane does not exist, the data set is _non-separable_. What then?

It is still possible to reconstruct a hyperplane and use it for classification, but then we have to _allow some misclassification_ in the training data.

* Soft margin classifier = support vector classifier.
* Allowing some misclassifications make the class boundaries more robust to future observations.

## Optimization problem

$$\text{maximize}_{\beta_0,\beta_1,\dots,\beta_p}M$$
$$\text{subject to }\sum_{j=1}^p{\beta_j}^2=1$$
$$y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i1}+\dots+\beta_px_{ip})\geq M(1-\epsilon_1),\forall i=1,\dots,n.$$
$$\epsilon_i\geq0,\sum_{i=1}^n{\epsilon_i}\leq C.$$

We get more boundary violations, $\epsilon$ is budget.

* M is the width of the margin
* $\epsilon_1,\dots,\epsilon_n$ are slack variables.
  * If $\epsilon_i=0$ it means that observations $i$ on the correct side of the margin,
  * if $\epsilon_i>0$ obsrvation $i$ is on the wrong side of the margin.
  * if $\epsilon_i>1$ obsrvation $i$ is on the wrong side of the hyperplane.

__Classifiation rule__

* The hyperplane has the property that it __only__ depends on the observation that __either lie on the margin or on the weing side of the margin.__ In fact, a noteworthy property of the solution is that

$$\hat{\beta}=\sum_{i\in S}{\hat{\alpha_i}x_i},$$
where $S$ si a _support set._
* The observations $x_i,i\in S$ are called our __support vectors.__
* The observation on the correct side of the margin do not affect 

## Role of the tuning parameter $C$

large $C\implies$ wider boundary.

```{R}
svmfit_linear1 = svm(y ~ ., data=train2, kernel="linear", cost=1, scale=False)
```

`cost=1/C`

## Observations
* The crosses in the plot indicate the support vectors, whose index can be obtained from

`svmfit_linear1$index`

## Cross validation to find an optimal cost

The `cost` is a tuning parameter. By using the `tune()` function we can perform 10-fold cross-validation and find the cost-parameter that vives the lowest error cross-validation error:

```
CV_linear = tune(svm(y ~ ., data=train2, kernel="linear", ranges = list(cost = c(0.1, ...)), scale=False))
```

## Support Vector Machines

* For some dayasets a _non-linear decision boundary_ between the classes is more suitable than a linear decision boundary.
* In such cases you can use a __Support Vector Machine (SVM)__. This is an extension of the support vector classifier.

## Expanding the feature space

* Recall from Module 7: We could fit non-linear regression curves by using a polynomial basis. This was a linear regression in the transformed variables, but non-linear in the original variables.
* __Idea:__ Find a linear boundary in that high-dimensional space using
  * higher-order terms $X_i^k$
  * interaction terms $X_iX,$
  * ...

## Problems and better ideas
* Computation using polynomials quickly becomes unmanagebable.
* Higher-order polynomials become very wiggly and wild.
* More elegant idea is the use of _kernels._
* But first we have to undertand the role of __inner product__

## Inner product

$$\langle x_i, x_{i'}\rangle=\sum_{j=1}^p{x_{ij}x_{i'j}}.$$

The inner product encodes for the _similarity_ between observations.

The solution function $\hat{f}$ to the support vector classifier problem at a new observation $x,$ can be expressed as

$$\begin{aligned}
\hat{f}(x)&=\hat{\beta_0}+x^T\hat{\beta}\\
&=\hat{\beta_0}+\sum_{i\in S}\hat{\alpha_i}\langle x,x_i\rangle,
\end{aligned}$$

where $\alpha_i$ is some parameter and $i=1,\dots,n.$

To estimate the parameters $\beta_0,\alpha_1,\dots,\alpha_n$ we only need to know the $\binom{n}{2}$ innter products for $\langle x_i,x_i'\rangle$ between all pair of training observations (and their correct class)

## Kernels

* Idea: replace the innerproduct $\langle x_i, x_i'\rangle$ as measure of similarity by a more general concept. Replace $\langle x_i,x_i'\rangle$ by a _kernel_ $K(x_i, x_i'),$ such that

$$f(x)=\beta_0+\sum_{i\in S}\alpha_iK(x, x_i)$$

* For the familiar linear case, the kernel is simply the inner product (_linear kernel_) $K(x_i,x_i')=\sum_{j=1}^px_{ij}x_{i'j}$.
* If we want a more flexible decision boundary we could instead use a _polynomial kernel_ of degree $d>1:$

$$K(x_i, x_i')=(1+\sum_j^p{x_{ij}x_{i'j}})^d$$

* By using a _non-linear kernels_m the resulting classifier is a _support vector machine._
* The nice things here is that we only need to calculate the kernels, not the basis functions.
* We ony need to compute $K(x_i, x_{i'})$ for the $\binom{n}{2}$ distinct pairs - without explicitly working in an enlarged feature space. THis is very useful when there are _many features,_ i.e. $p\geq n.$

## The radial kernel

* A very popular choice is the _radial kernel,_

$$K(x_i, x_i')=\exp(-\gamma\sum_{j=1}^p(x_{ij}-x_{i'j})^2),$$
where $\gamma$ is a positive constant (a tuning parameter)
* Interestingly, the radial kernel computes the inner product in a infinite dimensional feature space. But, this does not give overfitting vause some of the dimensions are "squashed doen"
* We have the parameter $\gamma$ and the budget parameter $C$

* The radial kernel is convenient if we want a circular decision boundary.
* $\gamma$ and our budget $C$ can be chosen by cross-validation.

## Kernels and our optimization

$$\text{maximize}_{\beta_0,\beta_1,\dots,\beta_p}M$$
$$\text{subject to }\sum_{j=1}^p{\beta_j}^2=1$$
$$y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i1}+\dots+\beta_px_{ip})\geq M(1-\epsilon_1),\forall i=1,\dots,n.$$
$$\epsilon_i\geq0,\sum_{i=1}^n{\epsilon_i}\leq C.$$
where
$$f(x_i)=\beta_0+\sum_{l=1}^n{\alpha_l K(x_i, x_l)}$$

$$\gamma=\frac{1}{\sigma^2}$$

## Extensions

### More than two classes

What if we habe $k$ classes? Two popular approaches:

* OVA: _one-versus-all_ Fit $k$ different two-class SVMs $f_k(x)$ where, each, time, ine class is compared to the ensemble of all other classes. Classify a test observation to the class where $f_k(x*)$ is largest.
* OVO: _one-versus-one._ `libsvm` uses this approach, in which $k(k-1)/2$ binary clasifiers are trained; the appropiate class is found by a voting scheme (the class that wins the most pairwise competitions for a given $x*$ is chosen)

## Comparisons: SVM and logistic regression

$$\text{minimize}_\beta\{\sum_{i=1}^n{\max(0,1-y_if(x_i))+\lambda\sum_{j=1}^p{\beta_j}^2}\}$$

* The loss 4L(x,y,\beta)$ is called _hinge loss_ - observte the max and $0$ to explain why only support vectors contribute.
* The penalty is a ridge penalty.
* 

Interestingly, the loss functions for a support vector classifier with $f(X)=\beta_0+\beta_1X_1+\dots+\beta_pX_p$ and for logistic regression using the same set of predictors _look very similar:_

__Hinge loss:__

$$\max(0,1-y_if(x_i))$$

* For ocmparison a logisitc regression loss function would be (binomial deviance with $-1,1$ coding of $y$)

$$\log(1+\exp(-y_if(x_i)))$$

* In logistic regression all observations contribute weighted by $p_i(1-p_i)$ (where $p_i$ is probability for class $1$), that fade smoothly.

## When to use SVM?

* If classe are nearly separable SVM will perform better than logsitc regression. Also LDA will perform better than logistic regression.
* Otherwise, a ridge penalty version of logistic regression is __very similar__ to SVM, and logistic regression will also give you probabilites for each class
* If class boundaries are non-linear then SVM is more popular, but _kernel versions of logistic regression_ are also possible, but more computationally expensive (and traditionally less used).
* Drawbacks of SVMs: No feature selection, no probabilites, thus in general interpretability is difficult.

