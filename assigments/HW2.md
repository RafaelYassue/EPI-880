**Homework 2**

In Example 1.2 we evaluate the effects of the number of markers used on prediction accuracy.  In this example, models are build by 
first ranking markers based on GWAS and then estimating effects using OLS for the top 1, 2,….,300 markers. 
Each of these models are then evaluated based on their prediction accuracy in the testing set. 

We observed that prediction accuracy grows up to a point and then declines, probably due to increase variance of the OLS estimates.
In our second module we discussed Ridge Regression as a way to reduce the variance of estimates. In this HW we looked at combining both variable selection
and shrinkage estimation. 

**Assignment**:

- Replace the OLS estimation with RR estimates (for that you can use the code below)
- Produce a plot with R-squared versus number of markers with results obtained using both OLS and Ridge Regression. 
- Submit by Wednesday July 20th, 10:00am  a ppt or a pdf with your final plot and your code.

Note: I’ll randomly draw a name and this person will need to explain what he/she did and discuss the results.



```R

# A non-otimized function to get RR estimates

getRR<-function(y,X,lambda=NULL,h2=.5){
        X=scale(X)
        p=ncol(X)
	y=y-mean(y)

	if(is.null(lambda)){
		lambda=p*(1-h2)/h2
	}

	C<-crossprod(X)
	diag(C)=diag(C)+lambda
	rhs=crossprod(X,y)
	CInv=chol2inv(chol(C))
	sol=crossprod(CInv,rhs)

	return(sol)
}

# Example

 library(BGLR)
 data(wheat)
 X=wheat.X
 y=scale(wheat.Y[,1],center=TRUE,scale=FALSE) 
 
 bRR=getRR(y=y,X=X)
 cor(y,X%*%bRR)
 
```


**A note on scaling in Ridge Regression**

In the linear regression model

		y=Xb+e

Ridge regression estimates are obtained as the solution to the following optimization problem

		bRR=argmin{   (y-Xb)'(y-Xb) + lambda*b'b

where y, X and b are a vector of phenotypes, a matrix of covariates and a vector of effects, respectively, and lambda is the ridge-regression constant, also called regularization or shrinkage parameter.


The solution can be shown to be


		bRR=[X'X + I*lambda ]^-1X'y
 

The diagonal elements of X'X are the sum of squares of each of the columns of X. These sum of squares depend on the variance of each column. Since the constant that we add in the diagonal (lambda) is the same, we usually put all the columns of X into the same scale; otherwise there can be extremely different level of shrinkage for different effects. Thereofre, usually before fitting Ridge Regression we set

		X=scale(X)
	
We will discuss in the course how to estimate lambda from data. For now we need to use a rule of thumb that allows us to compute 'sensible' RR estimates. We can guess lambda as a function of the R-squared of the model (h2, or gnomic heritability in genetic applications).

If you just scaled each column of X to unit variance, the sum of the variances of the columns is p (the number of markers) then you can use  lambda=p*(1-h2)/h2.  If you further divided X by the square-root of p, then the sum of the variances of the columsn of X is one, and you can use lambda=(1-h2)/h2.

We will further discuss and explain this in class.
