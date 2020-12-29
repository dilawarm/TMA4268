# Section 9 - Dimension Reduction
## Dimension Reduction Methods
* The methods that we have discussed so far in this chapter have involved fitting linear regression models, via least squares or a shrunken approach, using the original predictors, $X_1,X_2,\dots,X_p.$
* We now explore a class of approaches that _transform_ the predictors and then fit a least squares model using the transformed variables. We will refer to these techniques as _dimension reduction_ methods.
## Dimension Reduction Methods: details
* Let $Z_1,Z_2,\dots,Z_M$ represent $M<p$ _linear combinations_ of our original $p$ predictors. That is,
$$Z_m=\sum_{j=1}^p{\phi_{mj}X_j}\tag{1}$$
for some constants $\phi_{m1},\dots,\phi_{mp}.$
* We can then fit the linear regression model,
$$y_i=\theta_0+\sum_{m=1}^M{\theta_mz_{im}+\epsilon_i,\quad i=1,\dots,n},\tag{2}$$
using ordinary least squares.
* Note that in model $(2),$ the regression coefficients are given by $\theta_0,\theta_1,\dots,\theta_M.$ If the constants $\phi_{m1},\dots,\phi_{mp}$ are chosen wisely, then such reduction approaches can often outperform OLS regression.
* Notice that from definition $(1),$
$$\sum_{m=1}^M{\theta_mz_{im}}=\sum_{m=1}^M{\theta_m\sum_{j=1}^p{\phi_{mj}x_{ij}}}=\sum_{j=1}^p{\sum_{m=1}^M{\theta_m\phi_{mj}x_{ij}}}=\sum_{j=1}^p{\beta_jx_{ij}},$$
where
$$\beta_j=\sum_{m=1}^M{\theta_m\phi_{mj}}.\tag{3}$$
* Hence model $(2)$ can be thought of as a special case of the original linear regression model.
* Dimension reduction serves to constrain the estimated $\beta_j$ coefficients, since now they must take the form $(3).$
* Can win in the bias-variance tradeoff.