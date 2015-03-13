**ptucker-em** is an R implementation of pTucker (Chu and Ghahramani, 2010) with the EM algorithm. Like PCA or matrix factorization, ptucker-em finds a low-dimensional representation of a given data array (tensor).

Keywords: tensor decomposition, missing-values prediction (imputation), feature extraction, Bayesian probabilistic model.



# Installation #
  1. Install `tensorA` package from CRAN. Run `R` and type
```
> install.packages("tensorA")
```
  1. Include the source code `pTucker.R`.
```
> source("path/to/pTucker.R")
```

# Usage #

## Model fitting ##
`pTucker(X, ranks, ...)` performs to solve a data array `X` by a pTucker model in which the dimensions are specified by `ranks`.

Details of the arguments of `pTucker()`:
  * X: An L-dimensional array. 'NA' elements are considered as missings. 'L' can be 2 (i.e. X is matrix ), 3, 4, ...
  * ranks: An L-dimensional vector or string which specifies dimensions of the core tensor (i.e. the feature space). If 'ranks' is string, it must be like "4x3x5", which is equivalent as `c(4,3,5)`.
  * sigma: A scalar. The noise variance.
  * gamma: An L-dimensional vector. The regularization coefficients of each factor.
  * max.itr: The number to stop the EM iteration.
  * is.update.sigma: If TURE, then 'sigma' is updated during the EM iterations.
  * tr: if tr >= 1 then display the value of lowerbound (expected log-likelihood) in each iteration.

## Prediction ##
After fitting the model, `predict()` computes predictive values.

## Toy example ##
```
source("pTucker.R")

dims <- rep(10, 3)            # X is a 10x10x10 data tensor
ranks <- rep(3, length(dims)) # dimensions of feature space are 3x3x3
obs.ratio <- 0.5              # Half of elements of X are missing

# the number of elements in X
D.N <- prod(dims)
  
# generate synthetic data
X.full <- generate.data(dims, ranks)

# fill some of elements as missing
X <- X.full
test.ind <- sample(D.N, ceiling(D.N * (1 - obs.ratio)))
X[test.ind] <- NA

# compute pTucker and predict the missing values
res <- pTucker(X, ranks)
X.pred <- predict(res)

# display Root-Mean-Square-Error
print(c(RMSE = sqrt(mean(c(X.full[test.ind] - X.pred[test.ind])^2))))

# visualize extracted low-dimensional features (10x3) of the 1st mode
image(res$U[[1]])
```

# Notes #
The original algorithm of pTucker proposed in (Chu and Ghahramani, 2010) directly optimizes the marginal likelihood. Although its objective function is the same as that of ptucker-em, the solutions of them would be different local optima.

# References #
  * Probabilistic Models for Incomplete Multi-dimensional Arrays. Wei Chu, Zoubin Ghahramani; JMLR W&CP 5:89-96, 2009. [PDF](http://jmlr.csail.mit.edu/proceedings/papers/v5/chu09a/chu09a.pdf)