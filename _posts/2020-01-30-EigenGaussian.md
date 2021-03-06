---
title: 'How Eigen decomposition affect 2d Gaussian Distribution'
date: 2020-01-30
published: true
tags:
  - Linear Algebra
  - Probability
---

How eigendecomposition (Spectral decompostion) could help me understand 2d gaussian distribution geometrically?

Introduction
------
I have not realized how powerful eigendecomposition when I first learn about it in my first year linear algebra courses. It took me about 2 years learning Machine Learning and Probability to find the beauty about eigendecomposition. For example, If you understand what eigenvalues and eigenvectors are, then you will know what Principle Components Analysis (PCA) is doing, why it could reduce the dimensionality of complex datasets and why principle components satisfy independent assumptions.  
I will not talk about eigendecompostion from the very beginning. Instead, I would like to suggest you to take a look of one of favourite youtube channel ["3Blue1Brown"](https://www.youtube.com/channel/UCYO_jab_esuFRV4b17AJtAw). Their videos are extremely easy to understand, I even suggest some junior undergraduate students to watch their videos intend to enhance their understands. Here is the [link if you want to know what eigenvalues and eigenvectors truly are.](https://www.youtube.com/watch?v=PFDu9oVAE-g&t=551s)

2D Gaussian distribution
------
Now recall a 1D Gaussian distribution, aka Normal distribution. Its' probability density function is written as:  
  
$$P(x) = \frac{1}{\sigma \sqrt{2 \pi}}e^{ - ({x - \mu })^2 /2 \sigma ^2 }$$
  
To remind you about the most two important parameters of Gaussian distribution, the $$\mu$$(mean) measures the location of our gaussian distribution and the $$\sigma$$(std error) measures the spread of our distribution. Since both parameters are 1D scalar. It is not hard to understand how they measure our distribution.

![1D Gaussian](https://ned.ipac.caltech.edu/level5/Leo/Figures/figure3.jpeg)

However, as we increase the dimension of our $$x$$ to 2. Our 2d Gaussian distribution's pdf is written as:

$$P(x) = \frac{1}{\sqrt{2 \pi} \left | \Sigma \right |^{1/2}}e^{ -\frac{1}{2} ({x - \widetilde{\mu} })^T \Sigma^{-1}  (x- \widetilde{\mu}) }$$

Now our $$\Sigma$$ will be a 2x2 matrix and $$\widetilde{\mu}$$ will be a 2x1 vector. It is harder to interpret 2d distribution geometrically. But remember our topic of this context. We could use eigen decomposition to help us.

As you could find the $$\mu$$ only decide where our 2D distribution locate. The shape of our distribution only dependends on $$\Sigma$$. 

![2D Gaussian](https://scipython.com/static/media/uploads/blog/multivariate_gaussian/bivariate_gaussian.png)

Remember one important pattern for our $$\Sigma$$ matrix, it is a real symmetric Matrix (Since $$cov(x_i, x_j) = cov(x_j, x_i)$$). If we apply a eigendecompostion to $$\Sigma$$ matrix:

$$\Sigma = QVQ^{-1} \\ = QVQ^{T}$$

Now there are some important features we need to specify for $$Q$$ and $$V$$.

* $$V$$ is a diagonal matrix where the elements are eigen values corresond to eigenvectors in $$Q$$, s.t. $$V_{ii}$$ is the eigenvalue for $$Q_i$$.
* $$Q$$ is a matrix with orthogonal column vectors. s.t. $$Q_i Q_j^{T} = I$$ with $$i \neq j$$

If you have aware of the image of 2D gaussian distribution when x1 and x2 are independent, which $$\Sigma$$ is a diagonal matrix. It's graph looks like this.

![Independent Covariance matrix 2D Gaussian](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post1/2DG-1.png)

Where 
$\Sigma = \begin{bmatrix} 2 & 0 \\\ 0 & 2 \end{bmatrix}^{T}$ , and 
$\mu = \begin{bmatrix} 0 & 0 \end{bmatrix}^{T}$

If we did a eigendecompostion to it, we could easily digest 
$Q = \begin{bmatrix} 1 & 0 \\\ 0 & 1 \end{bmatrix}$, and $V = \begin{bmatrix} 2 & 0 \\\ 0 & 2 \end{bmatrix}$. You may aware of it right now, the eigenvectors are along x&y-axis and the eigenvalues are the same. s.t. [1, 0] and [0, 1]. Is that really a coincident? How about we try some other covariance matrix. Let's say $\Sigma = \begin{bmatrix} 2 & 0 \\\ 0 & 4 \end{bmatrix}$.
Now it is no longer circular. Since our $V = \begin{bmatrix} 2 & 0 \\\ 0 & 4 \end{bmatrix}$. 

![Independent Covariance [[2 0], [0, 4]] matrix 2D Gaussian](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post1/2DG-2.png)

But our eigenvectors keep the same so our ellipse still lie along x&y-axis. If we remove the independence of x1and x2, let's set $\Sigma = \begin{bmatrix} 5 & 2 \\\ 2 & 2 \end{bmatrix}$. You could see the rotation to our ellipse. This was caused by the eigenvectors. As you could see now $Q = \begin{bmatrix} 2 & 1 \\\ -1 & 2 \end{bmatrix}$. It is no longer x&y-axis alligned.

![Dependent Covariance [[5 2], [2, 2]] matrix 2D Gaussian](https://github.com/superp0tat0/superp0tat0.github.io/raw/master/files_posts/post1/2DG-3.png)

To conclude our findings, the shape of our 2d Gaussian distribution is decided by our covariance matrix. More specifically, the directions of major and minor axis of our ellipse are decided by our eigenvectors. The length of major and minor axis are decided by our eigenvalues. This could lead to some interesting facts.
* If $$C$$ is a singular matrix, then our 2D gaussian distribution will actually be a 1D gaussian distribution
* $$C$$ could also decide the orientation of our ellipse
  * when $C_{12} = C_{21} > 0$, it is oriented up to the right
  * when $C_{12} = C_{21} < 0$, it is oriented up to the left.

References:<br />
1. 1D Gaussian image: https://ned.ipac.caltech.edu/level5/Leo/Stats2_3.html<br />
1. 2D Gaussian image: https://scipython.com/blog/visualizing-the-bivariate-gaussian-distribution/<br />
1. Some tutorial notes for CSCC11 (Introduction to Machine learning and Data Mining ) taught by [Prof. David J Fleet](http://www.cs.toronto.edu/~fleet/)
