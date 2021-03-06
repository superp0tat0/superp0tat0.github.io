---
title: 'How Ridge/Lasso Regression could solve overfit from a probability prospective?'
date: 2020-02-13
tags:
  - Machine Learning
  - Probability
  - Regulizer
  - Overfit
---

Q: Why do we need Ridge and Lasso Regression, and how they work intuitively? 

Introduction to Overfit
------
To fit a model, we need to introduce parameters to our model intend to fit the observations as accurate as possible. However, if we introduce more parameters than the distribution where our observations follow. We may encounter overfitting problem.
To define overfitting, you could think our model is too "powerful", it could explain even the noise in our observations. The reason why overfit is bad for our prediction is simple. We want our model to be able to generalize the prediction, so it could predict future values based on experience. While overfit will hurt this generalization ability. Given an extreme case, my model could give the 100% accurate value based on the input, but only for the data it has seen. It will give a random guess for the data it does not seen. Then my model is no better than a simple disk, Where the disk is also memorizing data.

Simple Overfit Example
------
How about we give a simple coding example to explain why overfit will hurt your model:
First import all the libraries that are required:

```python
import numpy as np
import random as random
import matplotlib.pyplot as plt
from sklearn.preprocessing import PolynomialFeatures
from sklearn.linear_model import Ridge
```

Then create our model for fitting and visualization, here we only use Ridge regression (polynomial regression with l2 regulizer):

```python
class Demo_model:
    def __init__(self, w, mu, sig):
        self._x = np.expand_dims(np.arange(-10,10), axis=1)
        self._poly = np.poly1d(w)
        self._y = self.generate_data_1d(self._x, mu,sig)
        self._polywf = None
        self._ploywr2 = None
        self._ploywr2_value = None
    
    def plot(self):
        plt.plot(self._x, self._poly(self._x),'g') #The plot of original target function
        plt.plot(self._x, self._polywf(self._x),'b') #The plot of regression without any regulizer
        #plt.plot(self._x, self._ploywr2_value,'r') #The plot of ridge regression
        plt.show()
            
    def generate_data_1d(self, x, mu, sig):
        num_samples = x.shape[0]
        # Generate noise
        noise = np.random.normal(loc=mu, scale=sig, size=x.shape)
        # Compute outputs y, equal to x^T w plus the noise
        y = self._poly(self._x) + noise
        return y
    
    def train(self, poly, r2):
        #Fit without regulizer
        self._polywf = np.poly1d(np.polyfit(self._x.T[0], self._y.T[0], poly))
        
        #Fit with l2 regulizer with lamda
        po = PolynomialFeatures(poly, include_bias=True)
        x = po.fit_transform(self._x.reshape(1, -1).T)
        rg = Ridge(alpha = r2)
        self._ploywr2 = rg.fit(x,self._y)
        self._ploywr2_value = self._ploywr2.predict(x)
```

Then we could set our target function to be a relative simple function. In following example we set our $\sigma^2 = 10$, which is high.\\
$y = 0.1x^3 + 0.2x^2 + 0.1x + 4 + \epsilon$ where $\epsilon \sim \mathcal{N}(0,\sigma^2)$

```python
random.seed(101)
w = np.array([0.1,0.2,0.1,4])
model = Demo_model(w,0,10)
```

Next, train our model with degree up to 100, and set our $\lambda = 1$, we will talk about what $\lambda$ means later, right now it means nothing since we have comment the Ridge regression plot.

```python
model.train(100,1)
model.plot()
```

![Overfit](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post2/Riege_Original.png)

We could clearly see there is a significant error between the real function and our prediction function. Our model is too strong so it could fit the noise inside our model. To solve this problem, we could limit our hypothesis sets. Here you could think the hypothesis sets contains all the possibilities of our weight matrix. A larger hypothesis set will generally better than a smaller hypothesis set for reducing training error. But it will hurt model generalization ability.
So, to set our hypothesis set to be smaller, the train error will goes up but it will improve the model generalization ability.

```python
random.seed(101)
w = np.array([0.1,0.2,0.1,4])
model = Demo_model(w,0,10)
model.train(10,1)
model.plot()
```

![Less Overfit](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post2/RO2.png)

Now it is better, but still a little bit overfit. But now there is a practical problem. How do you know which degree will perform the best? Of course, you could loop all of them. But for some more complex functions the degree is not the only variable that you could manipulate. That is where the regularization comes out and make your life easier. It could automatically "smooth" your model.

What is Ridge regression and how they work?
------
To simply define what is a Ridge regression. I want to refer the formula to get the optimal weights matrix.<br />
$argmin_{W}\left \| Y-W^TX \right \|_{2}^{2}$<br />
Intuitively, we want to find the weight matrix that could minimize our squared loss. However, what should we do when we have multiple weight matrix that could achieve the "same minimum loss". Refer to a concept in philosophy: ["Occam's razor"](https://en.wikipedia.org/wiki/Occam%27s_razor), "Entities should not be multiplied without necessity.". We should choose the weight matrix that is the "simplest". In other word, we want to choose model that is not overact to our training set, the "smoothest" fit function.
Now, let's take a look of how Ridge Regression looks like:<br />
$$argmin_{W}\left \| Y-W^TX \right \|_{2}^{2} + \lambda\left \| W \right \|_{2}^{2}$$<br />
You could observe we add a new element after our original loss, the function of this element is when we got weight matrix that could get close loss. We will choose the weight matrix that is the simplest, where $\lambda$ controls how much "simple" we want our function to be. When we set $\lambda$ to be too large, our loss function will think complexity is very important and the loss does not important at all compare to complexity. It will make our function to be just a constant.

$\lambda = 1$            |  $\lambda = 10^{20}$
:-------------------------:|:-------------------------:
![](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post2/CR_1.png)  |  ![](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post2/CR_2.png)

The probability understanding of Ridge regression
-----
I believe most of the entry level machine learning courses will talk about overfit and Ridge regression. The problem I have is why Ridge regression work, does limit the norm of your weight matrix really solve overfit? On the first hand, yes, it is. However, there is a better approach to explain the ideology of Ridge regression, and this approach connects Bayes and Ridge regression. I am always surprised by some connections like this when I study machine learning by the way.

I will do a quick review of what are bayes(Naive Bayes) and maximum likelihood estimator.

**Naive Bayes** <br />
To conclude [Naive Bayes](https://en.wikipedia.org/wiki/Naive_Bayes_classifier), it is a simple but powerful equation that could combine prior (The knowledge you know before building the model etc hypothesis sets) and likelihood (The parameters likelihood comes from observations). Where the formula is defined as: <br />
![](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post2/naive_bayes.png) <br />
* Likelihood(Likelihood): The probability we observe our samples condition on a specific parameter.
* Class Prior Probability(Prior): The probability we have this specific parameter.
* Predicator Prior Probability(Data): The probability we got our observations
* Posterior Probability(Posterior):  The probability we have this specific parameter condition on we have observed this sample.

**Maximum Likelihood Estimator** <br/>
[Maximum Likelihood Estimator(MLE)](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation) is simply the approach we want to maximize the Likelihood as we mentioned above. As you could find, if we have an equal probability to all hypothesis, then maximum the posterior is the same as maximum the likelihood. Since we have no preference to anyone of the hypothesis.

Now let's move on the connection between Naive Bayes to Ridge regression. To maximum our posterior given a set of observations. We only need to maximize likelihood $$\times$$ prior since the data probability will hold for all the parameters.

To maximum the likelihood $$\times$$ prior. We could do some transformation to make our like easier. We define $$x_1,x_2, ... x_n$$ to be our observations and $$\beta$$ to be our hypothesis parameters, where we prefer $$\beta$$ to be a gaussian $$\beta \sim \mathcal{N}(0,\lambda^{-1})$$. We also define our target function to be: $$y_i = \beta x_i + \epsilon$$ where $$\epsilon \sim \mathcal{N} (0,\sigma^2)$$
Now our likelihood $$\times$$ prior will be a gaussian likelihood $$\times$$ gaussian prior: $$LP = \prod_{i=1}^{n}\mathcal{N}(y_i|\beta x_i,\sigma^2)\mathcal{N}(\beta|0,\lambda^{-1})$$.
To find $$\beta$$ maximize $$LP$$, we could find the equivalent $$\beta$$ satisfy the following:

$$
\begin{align*}
\beta &= argmax_{\beta}LP &\\ 
&= argmax_{\beta}(\prod_{i=1}^{n}\mathcal{N}(y_i|\beta x_i,\sigma^2)\mathcal{N}(\beta|0,\lambda^{-1})) &\\ 
&= argmax_{\beta}(log(\prod_{i=1}^{n}\mathcal{N}(y_i|\beta x_i,\sigma^2)\mathcal{N}(\beta|0,\lambda^{-1}))) &\\ 
&= argmax_{\beta}(\sum_{i=1}^{n}log(\mathcal{N}(y_i|\beta x_i,\sigma^2)) + log(\mathcal{N}(\beta|0,\lambda^{-1}))) &\\ 
&= argmax_{\beta}(\sum_{i=1}^{n}log(\frac{1}{\sigma \sqrt{2 \pi}}e^{ - ({y_i - \beta x_i })^2 /2 \sigma ^2 }) + log(\frac{1}{\lambda^{-2} \sqrt{2 \pi}}e^{ - ({y_i - \beta x_i })^2 /2 \lambda ^{-1} })) &\\ 
&= argmax_{\beta}(-\sum_{i=1}^{n} \frac{1}{2 \sigma^2}\left | y_i - \beta x_i \right |^{2} - \frac{\lambda}{2}\left | \beta \right |^{2} + C) &\\ 
&= argmin_{\beta}(\sum_{i=1}^{n} \frac{1}{\sigma^2}\left | y_i - \beta x_i \right |^{2} + \lambda \left | \beta \right |^{2} + C)
\end{align*}
$$


If we have $$\sigma^2 = 1$$, then we achieve:
$$\beta = argmin_{\beta}(\sum_{i=1}^{n} \left \| y_i - \beta x_i \right \|_{2}^{2} + \lambda \left \| \beta \right \|_{2}^{2} + C)$$
Which is exactly the same as ridge regression.

Now you should see the real function of what lambda does, it is the prior knowledge of the variance about $\beta$ follows a Gaussian distribution with 0 mean. With $$\lambda$$ to be higher, we are more convincing that $$\beta$$ will concentrate on 0 mean. It could smooth our function for sure, but the disadvantage is that it will make our function to be a constant ($$\beta = 0$$). However, if you are convinced to be smooth to a particular weight matrix $$\alpha$$. For example, $$\beta \sim \mathcal{N}(\alpha,\lambda^{-1})$$. Then we could set our Ridge regression to be:
$$argmin_{\beta}\left \| Y-\beta X \right \|_{2}^{2} + \lambda\left \| \beta - \alpha \right \|_{2}^{2}$$



