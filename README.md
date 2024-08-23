# BGRS-python
This code implements the asset pricing method described in Barillas, F., Shanken, J., 2018. "Comparing asset pricing models". Journal of Finance 73, 715–754.


## Description

To function, the method requires two functions: ML_r(Y|X) and ML_u(Y|X)

### ML_u(Y|X)

      Calculate the marginal likelihoods of the unrestricted linear regression model in BarillasShanken2018 eq. 9:
      Yt = α + Xt β + εt (non restricted)
      
      Yt : shape = (T x N)
      
      Xt : shape = (T x K)
      
      
      
      α ~ 1 x N
      β ~ K x N
      ε ~ T x N
      
      
      
      comparison with notation (in the code) and BarillasShanken2018 eq. 9:
      
      |F'F| = |X'X|
      with X'X : shape = (K x K)
      note that X MUST contains the market factor.
      
      |S| = |ε'ε|
      with ε'ε ~ (N x N)
      
      Q is constructed with the GRS score based on the regression Yt = α + Xt β + εt

### ML_r(Y|X)

    Calculate the marginal likelihoods of the restricted linear regression model in BarillasShanken2018 eq. 9:
    Yt = Xt β + εt (restricted)

    Yt : shape = (T x N)

    Xt : shape = (T x K)


    
    β ~ K x N
    ε ~ T x N

    Note that the function ML_r(Y|X) is used with different combination of factors and returns to input as Y and X.
    Therefore, we made a quick summary in the function ML_unrestricted to help.


    comparison with notation (in the code) and BarillasShanken2018 eq. 9:
    
    |F'F| = |X'X|
    with X'X : shape = (K x K)
    note that X MUST contains the market factor.

    |SR| = |ε'ε|
    with ε'ε ~ (N x N)

   


## The 3 BGRS methods

  

  Note that the function ML_u(Y|X) is used with different combination of factors and returns to input as Y and X.
  Therefore, we make a quick summary of what happens for 3 alternatives in the paper:

  I. Testing a pricing model against a general alternative ~ common α=0 test (close to GRS)

      BF=ML(H0)/ML(H1)=ML_r( Y = portfolio returns| X = [factors, Mkt] )/ML_u( Y = portfolio returns| X = [factors, Mkt] )

          note that we always make the distinction between factors and the market factor because the market is
          always included as exogenous variables. The associated linear regression here is :  rt = α + Ft β + εt    as defined in
          BarillasShanken2018 eq. 1 where F contains both non-market and market factors. 

      p = BF / (1 + BF) = "probability of observing our data under the null hypothesis (here: all alpha are zeros)"

  II. Relative model tests 

      BF abs/Mα = ML_r( Y = portfolio returns| X = [factors, Mkt] )/ML_u( Y = portfolio returns| X = [factors, Mkt] )
      
          This equation is the same as in I. Note that the difference with the equation below eq. 16 in BarillasShanken2018
          comes from a simplification as numerator and demoninator cancels each other. Also note that factors = [f, f*] 
          where f = included factors and f* = excluded factors.

          H0 : "all alpha are zeros"

      BF abs/M = ML_r( Y = f*| X = [f, Mkt] ) / ML_u( Y = f*| X = [f, Mkt] )
                  * ML_r( Y = portfolio returns| X = [f, Mkt] ) / ML_u( Y = portfolio returns| X = [f, Mkt] )

          Observe that this equation cover the following linear regression: [r, f*]t = α + [f,Mkt]t β + εt
          which is essentially two uncoupled linear regression: rt = α + [f,Mkt]t β + εt  and  [f*]t = α + [f,Mkt]t β + εt
          This is reflected in the fact that r and f* are not mixed when appearing in the marginal likelihoods.

          H0 : f is sufficient to price the returns (against the more general alternative [f,f*])

      BF rel = ( BF abs/M  ) /  ( BF abs/Mα ) = ML_r( Y = f*| X = [f, Mkt] )/ML_u( Y = f*| X = [f, Mkt] )

          Naturally, the relative bayesian factor does not involve r. 

          H0 : f is sufficient as pricing factors (against the more general alternative [f,f*])

          Note that BarillasShanken2018 explain that the conclusion regarding the two last propositions are related and the
          same which is illustrated by the fact that BF rel = ( BF abs/M  ) /  ( BF abs/Mα ).

  III. Simultaneous Comparison of All Models Based on a Set of Factors

      P(Mi|D)=MLi/sum{j}(MLj) 

      MLi = ML_u( Y = f| X = [None, Mkt] ) *  ML_r( Y = f*| X = [f, Mkt] ) * ML_r( Y = r| X = [f, f*, Mkt] )

          where f and f* are two complementary subsets of all the factors proper to model i. Note that the last term is common 
          to all models, therefore it can be dropped if we only intend to use MLi to compute P(Mi|D) as the common factors cancel
          each other. The fact that the returns no longer appear in the final score is not a problem since we observed an  
          analogous situation with BF rel and BF abs/Mα being closely related despite the absence of returns in the relative version.

      now you can compare the probability associated to each models (to each combination of included/excluded subsets of factors) and
      decide which one is better to use as pricing factors.






