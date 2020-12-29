# Section 3 - Backward Stepwise Selection
## Backward Stepwise Selection
* Likewise forward stepwise selection, _backward stepwise selection_ provides an efficient alternative to best subset selection.
* However, unlike forward stepwise selection, it begins with the full least squares model containing all $p$ predictors, and then iteratively removes the least useful predictor, one-at-a-time.
## Backward Stepwise Selection: details
### Backward Stepwise Selection
1. Let $\mathcal{M}_p$ denote the _full_ model, which contains all $p$ predictors.
2. For $k=p,p-1,\dots,1:$
   2.1 Consider all $k$ models that contain all but one of the predictors in $\mathcal{M}_k,$ for a total of $k-1$ predictors.
   2.2 Choose the _best_ among these $k$ models, and call it $\mathcal{M}_{k-1}.$ Here _best_ is defined as having smallest RSS or highest $R^2.$
3. Select a single best model from among $\mathcal{M}_0,\dots,\mathcal{M}_p$ using cross-validated prediction error, $C_p$ (AIC), BIC, or adjusted $R^2.$
## More on Backward Stepwise Selection
* Like forward stepwise selection, the backward selection approach searches through only $1+p(p+1)/2$ models, and so can be applied in settings where $p$ is too large to apply best subset selection.
* Like forward stepwise selection, backward stepwise selection is not guaranteed to yield the _best_ model containing a subset of the $p$ predictors.
* Backward selection requires that the _number of samples $n$ is larger than the number of variables $p$_ (so that the full model can be fit). In contrast, forward stepwise can be used when $n<p,$ and so is the only viable subset method when $p$ is very large.
## Choosing the Optimal Model
* The model containing all of the predictors will always have the smallest RSS and the largest $R^2,$ since these quantities are related to the training error.
* We wish to choose a model with low test error, not a model with low training error. Recall that training error is usually a poor estimate of test error.
* Therefore, RSS and $R^2$ are not suitable for selecting the best model among a collection of models with different numbers of predictors.