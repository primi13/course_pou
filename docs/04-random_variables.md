# Random variables {#rvs}

This chapter deals with random variables and their distributions.

The students are expected to acquire the following knowledge:

**Theoretical**

- Identification of random variables.
- Convolutions of random variables.
- Derivation of PDF, PMF, CDF, and quantile function.
- Definitions and properties of common discrete random variables.
- Definitions and properties of common continuous random variables.
- Transforming univariate random variables.

**R**

- Familiarize with PDF, PMF, CDF, and quantile functions for several distributions.
- Visual inspection of probability distributions.
- Analytical and empirical calculation of probabilities based on distributions.
- New R functions for plotting (for example, _facet_wrap_).
- Creating random number generators based on the Uniform distribution.



## General properties and calculations
\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:unnamed-chunk-2"><strong>(\#exr:unnamed-chunk-2) </strong></span>Which of the functions below are valid CDFs? Find their respective densities.
<span style="color:blue">R: Plot the three functions.</span>
  
a. 
\begin{equation}
  F(x) = \begin{cases}
            1 - e^{-x^2} & x \geq 0    \\
            0 & x < 0.
          \end{cases}
\end{equation}
            
b. 
\begin{equation}
  F(x) = \begin{cases}
           e^{-\frac{1}{x}} & x > 0    \\
            0 & x \leq 0.
          \end{cases}
\end{equation}
            
c. 
\begin{equation}
  F(x) = \begin{cases}
           0 & x \leq 0 \\
           \frac{1}{3} & 0 < x \leq \frac{1}{2} \\
           1 & x > \frac{1}{2}.
         \end{cases}
\end{equation}

</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. Yes.
  - First, let us check the limits. 
    $\lim_{x \rightarrow -\infty} (0) = 0$.
    $\lim_{x \rightarrow \infty} (1 - e^{-x^2}) = 1 - 
    \lim_{x \rightarrow \infty} e^{-x^2} = 1 - 0 = 1$.
  - Second, let us check whether the function is increasing. Let $x > y \geq 0$.
    Then $1 - e^{-x^2} \geq 1 - e^{-y^2}$.
  - We only have to check right continuity for the point zero. $F(0) = 0$ and
    $\lim_{\epsilon \downarrow 0}F (0 + \epsilon) = 
      \lim_{\epsilon \downarrow 0} 1 - e^{-\epsilon^2} =
       1 - \lim_{\epsilon \downarrow 0} e^{-\epsilon^2} =
      1 - 1 = 0$.
    - We get the density by differentiating the CDF. 
  $p(x) = \frac{d}{dx} 1 - e^{-x^2} = 2xe^{-x^2}.$ Students are encouraged to
  check that this is a proper PDF.
  

b. Yes.
  - First, let us check the limits. 
    $\lim_{x \rightarrow -\infty} (0) = 0 and 
    $\lim_{x \rightarrow \infty} (e^{-\frac{1}{x}}) = 1$.
  - Second, let us check whether the function is increasing. Let $x > y \geq 0$.
    Then $e^{-\frac{1}{x}} \geq e^{-\frac{1}{y}}$.
  - We only have to check right continuity for the point zero. $F(0) = 0$ and
    $\lim_{\epsilon \downarrow 0}F (0 + \epsilon) = 
      \lim_{\epsilon \downarrow 0} e^{-\frac{1}{\epsilon}} = 0$.
  - We get the density by differentiating the CDF. 
  $p(x) = \frac{d}{dx} e^{-\frac{1}{x}} = \frac{1}{x^2}e^{-\frac{1}{x}}.$ 
      Students are encouraged to
  check that this is a proper PDF.

c. No. The function is not right continuous as $F(\frac{1}{2}) = \frac{1}{3}$,
but $\lim_{\epsilon \downarrow 0} F(\frac{1}{2} + \epsilon) = 1$.
</div>\EndKnitrBlock{solution}

```r
f1 <- function (x) {
  tmp        <- 1 - exp(-x^2)
  tmp[x < 0] <- 0
  return(tmp)
}

f2 <- function (x) {
  tmp         <- exp(-(1 / x))
  tmp[x <= 0] <- 0
  return(tmp)
}

f3 <- function (x) {
  tmp           <- x
  tmp[x == x]   <- 1
  tmp[x <= 0.5] <- 1/3
  tmp[x <= 0]   <- 0
  return(tmp)
}

cdf_data <- tibble(x  = seq(-1, 20, by = 0.001),
                   f1 = f1(x),
                   f2 = f2(x),
                   f3 = f3(x)) %>%
  melt(id.vars = "x")

# geo_plot <- ggplot(data = data.frame(x = seq(-1, 10, by = 0.01)), aes(x = x)) +
#   stat_function(aes(color = "f1"), fun = f1) +
#   stat_function(aes(color = "f2"), fun = f2) +
#   stat_function(aes(color = "f3"), fun = f3) +
#   geom_hline(yintercept = 1)
# plot(geo_plot)

cdf_plot <- ggplot(data = cdf_data, aes(x = x, y = value, color = variable)) +
  geom_hline(yintercept = 1) +
  geom_line()
plot(cdf_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-4-1.png" width="672" />

\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:unnamed-chunk-5"><strong>(\#exr:unnamed-chunk-5) </strong></span>Let $X$ be a random variable with CDF 
\begin{equation}
  F(x) = \begin{cases}
            0 & x < 0 \\
            \frac{x^2}{2} & 0 \leq x < 1 \\
            \frac{1}{2} + \frac{p}{2} & 1 \leq x < 2 \\
            \frac{1}{2} + \frac{p}{2} + \frac{1 - p}{2} & x \geq 2
          \end{cases}
\end{equation}

a. <span style="color:blue">R: Plot this CDF for $p = 0.3$. Is it a discrete, 
continuous, or mixed random 
varible?</span>
  
b. Find the probability density/mass of $X$. 
</div>\EndKnitrBlock{exercise}

```r
f1 <- function (x, p) {
  tmp          <- x
  tmp[x >= 2]  <- 0.5 + (p * 0.5) + ((1-p) * 0.5)
  tmp[x < 2]   <- 0.5 + (p * 0.5) 
  tmp[x < 1]   <- (x[x < 1])^2 / 2
  tmp[x < 0]   <- 0
  return(tmp)
}

cdf_data <- tibble(x = seq(-1, 5, by = 0.001), y = f1(x, 0.3))
cdf_plot <- ggplot(data = cdf_data, aes(x = x, y = y)) +
  geom_hline(yintercept = 1) +
  geom_line(color = "blue")
plot(cdf_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-6-1.png" width="672" />
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. $X$ is a mixed random variable.

b. Since $X$ is a mixed random variable, we have to find the PDF of the
continuous part and the PMF of the discrete part. We get the continuous
part by differentiating the corresponding CDF, $\frac{d}{dx}\frac{x^2}{2} = x$. 
So the PDF, when $0 \leq x < 1$, is $p(x) = x$. Let us look at the discrete
part now. It has two steps, so this is a discrete distribution with two
outcomes -- numbers two and three. The first happens with probability 
$\frac{p}{2}$, and the second with probability $\frac{1 - p}{2}$. This reminds
us of the Bernoulli distribution, the only difference is that the probabilities
of outcomes are halved, as they need to be suitably normalized. So the PMF
for the discrete part is $P(X = x) = (\frac{p}{2})^{2 - \lfloor x \rfloor} 
(\frac{1 - p}{2})^{\lfloor x \rfloor - 1}$.</div>\EndKnitrBlock{solution}


\BeginKnitrBlock{exercise}\iffalse{-91-67-111-110-118-111-108-117-116-105-111-110-115-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:conv"><strong>(\#exr:conv)  \iffalse (Convolutions) \fi{} </strong></span>Convolutions are probability distributions that correspond to sums of
independent random variables.

a. Let $X$ and $Y$ be independent discrete variables. 
Find the PMF of $Z = X + Y$. Hint: Use the law of
total probability.

b. Let $X$ and $Y$ be independent continuous variables. 
Find the PDF of $Z = X + Y$ 
Hint: Start with the CDF.
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  P(Z = z) &= P(X + Y = z) & \\ 
           &= \sum_{k = -\infty}^\infty P(X + Y = z | Y = k) P(Y = k) & \text{ (law of total probability)} \\
           &= \sum_{k = -\infty}^\infty P(X + k = z | Y = k) P(Y = k) & \\
           &= \sum_{k = -\infty}^\infty P(X + k = z) P(Y = k) & \text{ (independence of $X$ and $Y$)} \\
           &= \sum_{k = -\infty}^\infty P(X = z - k) P(Y = k). &
\end{align}
  
b. Let $f$ and $g$ be the PDFs of $X$ and $Y$ respectively.
\begin{align}
  F(z)     &= P(Z < z) \\ 
           &= P(X + Y < z) \\ 
           &= \int_{-\infty}^{\infty} P(X + Y < z | Y = y)P(Y = y)dy \\
           &= \int_{-\infty}^{\infty} P(X + y < z | Y = y)P(Y = y)dy \\
           &= \int_{-\infty}^{\infty} P(X + y < z)P(Y = y)dy \\
           &= \int_{-\infty}^{\infty} P(X < z - y)P(Y = y)dy \\
           &= \int_{-\infty}^{\infty} (\int_{-\infty}^{z - y} f(x) dx) g(y) dy
\end{align}
Now
\begin{align}
  p(z) &= \frac{d}{dz} F(z) & \\
       &= \int_{-\infty}^{\infty} (\frac{d}{dz}\int_{-\infty}^{z - y} f(x) dx) g(y) dy & \\
       &= \int_{-\infty}^{\infty} f(z - y) g(y) dy & \text{ (fundamental theorem of calculus)}.
\end{align}</div>\EndKnitrBlock{solution}


## Discrete random variables
\BeginKnitrBlock{exercise}\iffalse{-91-66-105-110-111-109-105-97-108-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:bincdf"><strong>(\#exr:bincdf)  \iffalse (Binomial random variable) \fi{} </strong></span>Let $X_k$, $k = 1,...,n$, be random variables with the Bernoulli measure as the 
PMF with $p = 0.4$. Let $X = \sum_{k=1}^n$.

a. We call $X_k$ a Bernoulli random variable with parameter $p \in (0,1)$. 
Find the CDF of $X_k$.

b. Find PDF of $X$. 
This is a Binomial random variable with support in $\{0,1,2,...,n\}$ and
parameters $p \in (0,1)$ and $n \in \mathbb{N}_0$. We denote
\begin{equation}
  X | n,p \sim \text{Binomial}(n,p).
\end{equation}

c. Find CDF of $X$. 

d. <span style="color:blue">R: Simulate from the binomial distribution
with $n = 10$ and $p = 0.5$, and from $n$ Bernoulli distributions with
$p = 0.5$. Visually compare the sum of Bernoullis and the binomial. Hint:
  there is no standard function like _rpois_ for a Bernoulli random
variable. Check exercise \@ref(exr:binomialpmf) to find out how to sample 
from a Bernoulli
distribution.</span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. There are two outcomes -- zero and one. Zero happens with probability
$1 - p$. Therefore
\begin{equation}
  F(k) = \begin{cases}
            0 & k < 0 \\
            1 - p & 0 \leq k < 1 \\
            1 & k \geq 1.
          \end{cases}
\end{equation}
            
b. For the probability of $X$ to be equal to some $k \leq n$, exactly $k$
  Bernoulli variables need to be one, and the others zero. So $p^k(1-p)^{n-k}$.
There are $\binom{n}{k}$ such possible arrangements. Therefore
\begin{align}
  P(X = k) = P(\sum X_k = 2) = \binom{n}{k} p^k (1 - p)^{n-k}.
\end{align}
  
c. 
\begin{equation}
  F(k) = \sum_{i = 1}^{\lfloor k \rfloor} \binom{n}{i} p^i (1 - p)^{n - i}
\end{equation}      
            </div>\EndKnitrBlock{solution}

```r
set.seed(1)
nsamps        <- 10000
binom_samp    <- rbinom(nsamps, size = 10, prob = 0.5)
bernoulli_mat <- matrix(data = NA, nrow = nsamps, ncol = 10)
for (i in 1:nsamps) {
  bernoulli_mat[i, ] <- rbinom(10, size = 1, prob = 0.5)
}
bern_samp <- apply(bernoulli_mat, 1, sum)
b_data    <- tibble(x    = c(binom_samp, bern_samp), 
                    type = c(rep("binomial", 10000), rep("Bernoulli_sum", 10000)))
b_plot    <- ggplot(data = b_data, aes(x = x, fill = type)) +
  geom_bar(position = "dodge")
plot(b_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-10-1.png" width="672" />


\BeginKnitrBlock{exercise}\iffalse{-91-71-101-111-109-101-116-114-105-99-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:geocdf"><strong>(\#exr:geocdf)  \iffalse (Geometric random variable) \fi{} </strong></span>A variable with PMF 
\begin{equation}
  P(k) = p(1-p)^k
\end{equation}
is a geometric random 
variable with support in non-negative 
integers. It has one positive parameter 
$p$. We denote
\begin{equation}
  X | p \sim \text{Geometric}(p)
\end{equation}

a. Derive the CDF of a geometric random variable.

b. <span style="color:blue">R: Draw 1000 samples from the geometric
distribution with $p$ = 0.3$ and compare their frequencies to
theoretical values.</span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  P(X \leq k) &= \sum_{i = 0}^k p(1-p)^i \\
            &= p \sum_{i = 0}^k (1-p)^i \\
            &= p \frac{1 - (1-p)^{k+1}}{1 - (1 - p)} \\
            &= 1 - (1-p)^{k + 1}
\end{align}
</div>\EndKnitrBlock{solution}

```r
set.seed(1)
geo_samp <- rgeom(n = 1000, prob = 0.3)
geo_samp <- data.frame(x = geo_samp) %>%
  count(x) %>%
  mutate(n = n / 1000, type = "empirical_frequencies") %>%
  bind_rows(data.frame(x = 0:20, n = dgeom(0:20, prob = 0.3), type = "theoretical_measure"))

geo_plot <- ggplot(data = geo_samp, aes(x = x, y = n, fill = type)) +
  geom_bar(stat="identity", position = "dodge")
plot(geo_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-12-1.png" width="672" />

\BeginKnitrBlock{exercise}\iffalse{-91-80-111-105-115-115-111-110-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:unnamed-chunk-13"><strong>(\#exr:unnamed-chunk-13)  \iffalse (Poisson random variable) \fi{} </strong></span>A variable with PMF 
\begin{equation}
  P(k) = \frac{\lambda^k e^{-\lambda}}{k!}
\end{equation}
 is a Poisson random 
variable with support in non-negative integers. It has one positive parameter 
$\lambda$, 
which also represents its mean value and variance (a measure of the deviation
of the values from the mean -- more on mean and variance in the next chapter). 
We denote
\begin{equation}
  X | \lambda \sim \text{Poisson}(\lambda).
\end{equation}
This distribution is usually the default choice for modeling counts. We have
already encountered a Poisson random variable in exercise \@ref(exr:geopoispmf),
where we also sampled from this distribution.

The CDF of a Poisson random variable is $P(X <= x) = e^{-\lambda} \sum_{i=0}^x \frac{\lambda^{i}}{i!}$.

a. <span style="color:blue">R: Draw 1000 samples from the Poisson
distribution with $p$ = 0.3$ and compare their empirical cumulative
distribution function with the theoretical CDF.</span>
</div>\EndKnitrBlock{exercise}

```r
set.seed(1)
pois_samp <- rpois(n = 1000, lambda = 5)
pois_samp <- data.frame(x = pois_samp)
pois_plot <- ggplot(data = pois_samp, aes(x = x, colour = "ECDF")) +
  stat_ecdf(geom = "step") +
  geom_step(data = tibble(x = 0:17, y = ppois(x, 5)), aes(x = x, y = y, colour = "CDF")) +
  scale_colour_manual("Lgend title", values = c("black", "red"))
plot(pois_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-14-1.png" width="672" />
\BeginKnitrBlock{exercise}\iffalse{-91-78-101-103-97-116-105-118-101-32-98-105-110-111-109-105-97-108-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:negbinpdf"><strong>(\#exr:negbinpdf)  \iffalse (Negative binomial random variable) \fi{} </strong></span>A variable with PMF 
\begin{equation}
  p(k) = \binom{k + r - 1}{k}(1-p)^r p^k
\end{equation}
is a negative binomial random 
variable with support in non-negative integers. It has two parameters 
$r > 0$ and $p \in (0,1)$. We denote
\begin{equation}
  X | r,p \sim \text{NB}(r,p).
\end{equation}

a. Let us reparameterize the negative binomial distribution with $q = 1 - p$.
Find the PDF of $X \sim \text{NB}(1, q)$. Do you recognize this distribution?

b. Show that the sum of two negative binomial random variables with the same
$p$ is also a negative binomial random variable. Hint: Use the fact that
the number of ways to place $n$ indistinct balls into $k$ boxes is
$\binom{n + k - 1}{n}$.

c. <span style="color:blue">R: Draw samples from $X \sim \text{NB}(5, 0.4)$
and $Y \sim \text{NB}(3, 0.4)$. 
Draw samples from $Z = X + Y$, where you use the parameters calculated in 
b). Plot both distributions, their sum, and $Z$ using _facet_wrap_. 
Be careful, as R uses a different 
parameterization _size_=$r$ and _prob_=$1 - p$.</span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  P(X = k) &= \binom{k + 1 - 1}{k}q^1 (1-q)^k \\
           &= q(1-q)^k.
\end{align}
This is the geometric distribution.
  
b. Let $X \sim \text{NB}(r_1, p)$ and $Y \sim \text{NB}(r_2, p)$.
Let $Z = X + Y$.
\begin{align}
  P(Z = z) &= \sum_{k = 0}^{\infty} P(X = z - k)P(Y = k) & \text{ if k < 0, then the probabilities are 0} \\
           &= \sum_{k = 0}^{z} P(X = z - k)P(Y = k) & \text{ if k > z, then the probabilities are 0} \\
           &= \sum_{k = 0}^{z} \binom{z - k + r_1 - 1}{z - k}(1 - p)^{r_1} p^{z - k} \binom{k + r_2 - 1}{k}(1 - p)^{r_2} p^{k} & \\
           &= \sum_{k = 0}^{z} \binom{z - k + r_1 - 1}{z - k} \binom{k + r_2 - 1}{k}(1 - p)^{r_1 + r_2} p^{z} & \\
           &= (1 - p)^{r_1 + r_2} p^{z} \sum_{k = 0}^{z} \binom{z - k + r_1 - 1}{z - k} \binom{k + r_2 - 1}{k}&
\end{align}
The part before the sum reminds us of the negative binomial distribution with
parameters $r_1 + r_2$ and $p$. To complete this term to the
negative binomial PDF we need $\binom{z + r_1 + r_2 -1}{z}$. So the
only thing we need to prove is that the sum equals this term. 
Both terms in the sum can be interpreted as numbers of ways to
place a number of balls into boxes. For the left term it is
$z-k$ balls into $r_1$ boxes, and for the right $k$ balls into
$r_2$ boxes. For each $k$ we are distributing $z$ balls in total. 
By summing over all $k$, we actually get all the
possible placements of $z$ balls into $r_1 + r_2$ boxes.
Therefore
\begin{align}
  P(Z = z) &= (1 - p)^{r_1 + r_2} p^{z} \sum_{k = 0}^{z} \binom{z - k + r_1 - 1}{z - k} \binom{k + r_2 - 1}{k}& \\
           &= \binom{z + r_1 + r_2 -1}{z} (1 - p)^{r_1 + r_2} p^{z}.
\end{align}
From this it also follows that the sum of geometric distributions with the same
parameter is a negative binomial distribution.

c. $Z \sim \text{NB}(8, 0.4)$.
</div>\EndKnitrBlock{solution}

```r
set.seed(1)
nsamps <- 10000
x      <- rnbinom(nsamps, size = 5, prob = 0.6)
y      <- rnbinom(nsamps, size = 3, prob = 0.6)
xpy    <- x + y
z      <- rnbinom(nsamps, size = 8, prob = 0.6)
samps  <- tibble(x, y, xpy, z)
samps  <- melt(samps)
ggplot(data = samps, aes(x = value)) +
  geom_bar() +
  facet_wrap(~ variable)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-16-1.png" width="672" />

## Continuous random variables
\BeginKnitrBlock{exercise}\iffalse{-91-69-120-112-111-110-101-110-116-105-97-108-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:expcdf"><strong>(\#exr:expcdf)  \iffalse (Exponential random variable) \fi{} </strong></span>A variable $X$ with PDF $\lambda e^{-\lambda x}$ is an exponential random 
variable with support in non-negative real numbers. It has one positive 
parameter $\lambda$. We denote
\begin{equation}
  X | \lambda \sim \text{Exp}(\lambda).
\end{equation}

a. Derive the CDF of an exponential random variable.

b. Derive the quantile function of an exponential random variable.

c. Calculate the probability $P(1 \leq X \leq 3)$, where $X \sim \text{Exp(1.5)}$.

d. <span style="color:blue">R: Check your answer to c) with a
simulation (_rexp_). Plot the probability in a meaningful way.</span>
  
e. <span style="color:blue">R: Implement PDF, CDF, and the quantile
function and compare their values with corresponding R
functions visually. Hint: use the size parameter in _geom_line_ to
make one of the curves wider.</span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  F(x) &= \int_{0}^{x} \lambda e^{-\lambda t} dt \\
       &= \lambda \int_{0}^{x} e^{-\lambda t} dt \\
       &= \lambda (\frac{1}{-\lambda}e^{-\lambda t} |_{0}^{x}) \\
       &= \lambda(\frac{1}{\lambda} - \frac{1}{\lambda} e^{-\lambda x}) \\
       &= 1 - e^{-\lambda x}.
\end{align}

b.
\begin{align}
  F(F^{-1}(x))               &= x \\
  1 - e^{-\lambda F^{-1}(x)} &= x \\
  e^{-\lambda F^{-1}(x)}     &= 1 - x \\
  -\lambda F^{-1}(x)         &= \ln(1 - x) \\
  F^{-1}(x)                  &= - \frac{ln(1 - x)}{\lambda}.
\end{align}

c. 
\begin{align}
  P(1 \leq X \leq 3) &= P(X  \leq 3) - P(X \leq 1) \\
                     &= P(X \leq 3) - P(X \leq 1) \\
                     &= 1 - e^{-1.5 \times 3} - 1 + e^{-1.5 \times 1} \\
                     &\approx 0.212.
\end{align}
</div>\EndKnitrBlock{solution}

```r
set.seed(1)
nsamps <- 1000
samps  <- rexp(nsamps, rate = 1.5)
sum(samps >= 1 & samps <= 3) / nsamps
```

```
## [1] 0.212
```

```r
exp_plot <- ggplot(data.frame(x = seq(0, 5, by = 0.01)), aes(x = x)) +
  stat_function(fun = dexp, args = list(rate = 1.5)) +
  stat_function(fun = dexp, args = list(rate = 1.5), xlim = c(1,3), geom = "area", fill = "red")
plot(exp_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-18-1.png" width="672" />

```r
exp_pdf <- function(x, lambda) {
  return (lambda * exp(-lambda * x))
}

exp_cdf <- function(x, lambda) {
  return (1 - exp(-lambda * x))
}

exp_quant <- function(q, lambda) {
  return (-(log(1 - q) / lambda))
}

ggplot(data = data.frame(x = seq(0, 5, by = 0.01)), aes(x = x)) +
  stat_function(fun = dexp, args = list(rate = 1.5), aes(color = "R"), size = 2.5) +
  stat_function(fun = exp_pdf, args = list(lambda = 1.5), aes(color = "Mine"), size = 1.2) +
  scale_color_manual(values = c("red", "black"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-19-1.png" width="672" />

```r
ggplot(data = data.frame(x = seq(0, 5, by = 0.01)), aes(x = x)) +
  stat_function(fun = pexp, args = list(rate = 1.5), aes(color = "R"), size = 2.5) +
  stat_function(fun = exp_cdf, args = list(lambda = 1.5), aes(color = "Mine"), size = 1.2) +
  scale_color_manual(values = c("red", "black"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-19-2.png" width="672" />

```r
ggplot(data = data.frame(x = seq(0, 1, by = 0.01)), aes(x = x)) +
  stat_function(fun = qexp, args = list(rate = 1.5), aes(color = "R"), size = 2.5) +
  stat_function(fun = exp_quant, args = list(lambda = 1.5), aes(color = "Mine"), size = 1.2) +
  scale_color_manual(values = c("red", "black"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-19-3.png" width="672" />


\BeginKnitrBlock{exercise}\iffalse{-91-85-110-105-102-111-114-109-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:unifpdf"><strong>(\#exr:unifpdf)  \iffalse (Uniform random variable) \fi{} </strong></span>Continuous uniform random variable with parameters $a$ and $b$ has the PDF 
\begin{equation}
  p(x) = \begin{cases}
           \frac{1}{b - a} & x \in [a,b] \\
           0 & \text{otherwise}.
         \end{cases}
\end{equation}
  

a. Derive the CDF of the uniform random variable.

b. Derive the quantile function of the uniform random variable.

c. Let $X \sim \text{Uniform}(a,b)$. Derive the CDF of the variable 
$Y = \frac{X - a}{b - a}$. This is the **standard uniform random variable**.

d. Let $X \sim \text{Uniform}(-1, 3)$. 
Find such $z$ that $P(X < z + \mu_x) = \frac{1}{5}$.

e. <span style="color:blue">R: Check your result from d) using 
simulation.</span>
  </div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}
a.
\begin{align}
  F(x) &= \int_{a}^x \frac{1}{b - a} dt \\
       &= \frac{1}{b - a} \int_{a}^x dt \\
       &= \frac{x - a}{b - a}.
\end{align}

b.
\begin{align}
  F(F^{-1}(p))                &= p \\
  \frac{F^{-1}(p) - a}{b - a} &= p \\
  F^{-1}(p)                   &= p(b - a) + a.
\end{align}

c.
\begin{align}
  F_Y(y) &= P(Y < y) \\
         &= P(\frac{X - a}{b - a} < y) \\
         &= P(X < y(b - a) + a) \\
         &= F_X(y(b - a) + a) \\
         &= \frac{(y(b - a) + a) - a}{b - a} \\
         &= y.
\end{align}

d.
\begin{align}
  P(X < z + 1) &= \frac{1}{5} \\
  F(z + 1)     &= \frac{1}{5} \\
  z + 1        &= F^{-1}(\frac{1}{5}) \\
  z            &= \frac{1}{5}4 - 1 - 1 \\
  z            &= -1.2.
\end{align}
</div>\EndKnitrBlock{solution}

```r
set.seed(1)
a         <- -1
b         <- 3
nsamps    <- 10000
unif_samp <- runif(nsamps, a, b)
mu_x      <- mean(unif_samp)
new_samp  <- unif_samp - mu_x
quantile(new_samp, probs = 1/5)
```

```
##       20% 
## -1.203192
```

```r
punif(-0.2, -1, 3)
```

```
## [1] 0.2
```


\BeginKnitrBlock{exercise}\iffalse{-91-66-101-116-97-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:betacdf"><strong>(\#exr:betacdf)  \iffalse (Beta random variable) \fi{} </strong></span>A variable $X$ with PDF 
\begin{equation}
  p(x) = \frac{x^{\alpha - 1} (1 - x)^{\beta - 1}}{\text{B}(\alpha, \beta)},
\end{equation}
where $\text{B}(\alpha, \beta) = \frac{\Gamma(\alpha) \Gamma(\beta)}{\Gamma(\alpha + \beta)}$ and 
$\Gamma(x) = \int_0^{\infty} x^{z - 1} e^{-x} dx$
  is a Beta random 
variable with support on $[0,1]$. It has two positive 
parameters $\alpha$ and $\beta$. Notation:
\begin{equation}
  X | \alpha, \beta \sim \text{Beta}(\alpha, \beta)
\end{equation}
It is often used in modeling rates. 
<!--The CDF of a Beta random variable is--> 
<!--\begin{equation}--> 
<!--  F(x) = \frac{\text{B}(x; \alpha, \beta)}{\text{B}(\alpha, \beta)},--> 
<!--\end{equation}--> 
<!--  where $\text{B}(x; \alpha, \beta) = \int_0^x t^{\alpha+1} (1 - t)^{\beta - 1} dt$.--> 


a. Calculate the PDF for $\alpha = 1$ and $\beta = 1$. What do you notice?

b. <span style="color:blue">R: Plot densities of the beta distribution
for parameter pairs (2, 2), (4, 1), (1, 4), (2, 5), and (0.1, 0.1).</span>

c. <span style="color:blue">R: Sample from $X \sim \text{Beta}(2, 5)$ and
compare the histogram with Beta PDF.</span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{equation}
  p(x) = \frac{x^{1 - 1} (1 - x)^{1 - 1}}{\text{B}(1, 1)} = 1.
\end{equation}
This is the standard uniform distribution.

</div>\EndKnitrBlock{solution}

```r
set.seed(1)
ggplot(data = data.frame(x = seq(0, 1, by = 0.01)), aes(x = x)) +
  stat_function(fun = dbeta, args = list(shape1 = 2, shape2 = 2), aes(color = "alpha = 0.5")) +
  stat_function(fun = dbeta, args = list(shape1 = 4, shape2 = 1), aes(color = "alpha = 4")) +
  stat_function(fun = dbeta, args = list(shape1 = 1, shape2 = 4), aes(color = "alpha = 1")) +
  stat_function(fun = dbeta, args = list(shape1 = 2, shape2 = 5), aes(color = "alpha = 25")) +
  stat_function(fun = dbeta, args = list(shape1 = 0.1, shape2 = 0.1), aes(color = "alpha = 0.1"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-23-1.png" width="672" />

```r
set.seed(1)
nsamps <- 1000
samps  <- rbeta(nsamps, 2, 5)
ggplot(data = data.frame(x = samps), aes(x = x)) +
  geom_histogram(aes(y = ..density..), color = "black") +
  stat_function(data  = data.frame(x = seq(0, 1, by = 0.01)), aes(x = x), 
                fun   = dbeta, 
                args  = list(shape1 = 2, shape2 = 5), 
                color = "red", 
                size  = 1.2)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-24-1.png" width="672" />


\BeginKnitrBlock{exercise}\iffalse{-91-71-97-109-109-97-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:gammapdf"><strong>(\#exr:gammapdf)  \iffalse (Gamma random variable) \fi{} </strong></span>A random variable with PDF 
\begin{equation}
 p(x) = \frac{\beta^\alpha}{\Gamma(\alpha)} x^{\alpha - 1}e^{-\beta x}
\end{equation}
is a Gamma random variable with support on the positive numbers and 
parameters shape $\alpha > 0$ and rate 
$\beta > 0$.
We write
\begin{equation}
  X | \alpha, \beta \sim \text{Gamma}(\alpha, \beta)
\end{equation}
and it's CDF is
\begin{equation}
  \frac{\gamma(\alpha, \beta x)}{\Gamma(\alpha)},
\end{equation}
where $\gamma(s, x) = \int_0^x t^{s-1} e^{-t} dt$.
It is usually used in modeling positive phenomena (for example insurance 
claims and rainfalls). 

a. Let $X \sim \text{Gamma}(1, \beta)$. Find the PDF of $X$. 
Do you recognize this PDF?

b. Let $k = \alpha$ and $\theta = \frac{1}{\beta}$. Find the PDF
of $X | k, \theta \sim \text{Gamma}(k, \theta)$. Most (?ALL)
random variables can be reparameterized, and sometimes a reparameterized
distribution is more suitable for certain calculations. The first
parameterization is for example usually used in Bayesian statistics, while this
parameterization is more common in econometrics and some other applied fields.
Note that you also need to pay attention to the parameters in statistical
software, so diligently read the help files when using functions like
_rgamma_ to see how the function is parameterized.

c. <span style="color:blue">R: Plot gamma CDF for random variables
with shape and rate parameters (1,1), (10,1), (1,10). </span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. 
\begin{align}
  p(x) &= \frac{\beta^1}{\Gamma(1)} x^{1 - 1}e^{-\beta x} \\
       &= \beta e^{-\beta x}
\end{align}
This is the PDF of the exponential distribution with parameter $\beta$.

b.
\begin{align}
  p(x) &= \frac{1}{\Gamma(k)\beta^k} x^{k - 1}e^{-\frac{x}{\theta}}.
\end{align}
</div>\EndKnitrBlock{solution}

```r
set.seed(1)
ggplot(data = data.frame(x = seq(0, 25, by = 0.01)), aes(x = x)) +
  stat_function(fun = pgamma, args = list(shape = 1, rate = 1), aes(color = "Gamma(1,1)")) +
  stat_function(fun = pgamma, args = list(shape = 10, rate = 1), aes(color = "Gamma(10,1)")) +
  stat_function(fun = pgamma, args = list(shape = 1, rate = 10), aes(color = "Gamma(1,10)"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-26-1.png" width="672" />

\BeginKnitrBlock{exercise}\iffalse{-91-78-111-114-109-97-108-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:normalpdf"><strong>(\#exr:normalpdf)  \iffalse (Normal random variable) \fi{} </strong></span>A random variable with PDF 
\begin{equation}
 p(x) = \frac{1}{\sqrt{2 \pi \sigma^2}} e^{\frac{(x - \mu)^2}{2 \sigma^2}}
\end{equation}
is a normal random variable with support on the real axis and 
parameters $\mu$ in reals and $\sigma^2 > 0$.
The first is the mean parameter and the second is the variance parameter.
Many statistical methods assume a normal distribution. We denote
\begin{equation}
  X | \mu, \sigma \sim \text{N}(\mu, \sigma^2),
\end{equation}
and it's CDF is
\begin{equation}
  F(x) = \int_{-\infty}^x \frac{1}{\sqrt{2 \pi \sigma^2}} e^{\frac{(t - \mu)^2}{2 \sigma^2}} dt,
\end{equation}
which is intractable and is usually approximated.
Due to it's flexibility
it is also one of the most researched distributions. For that reason 
statisticians often use transformations of variables or approximate
distributions with the normal distribution.

a. Show that a variable $\frac{X - \mu}{\sigma} \sim \text{N}(0,1)$. This
transformation is called standardization, and $\text{N}(0,1)$ is
a **standardized normal distribution**.

b. <span style="color:blue">R: Plot the normal distribution with
$\mu = 0$ and different values for the $\sigma$ parameter.</span>

c. <span style="color:blue">R: The normal distribution provides a good 
approximation for the Poisson distribution with a large $\lambda$. 
Let $X \sim \text{Poisson}(50)$.
Approximate $X$ with the normal distribution and compare it's density with
the Poisson histogram. What are the values of $\mu$ and $\sigma^2$ that
should provide the best approximation? Note that R function _rnorm_ takes
standard deviation ($\sigma$) as a parameter and not variance.</span></div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  P(\frac{X - \mu}{\sigma} < x) &= P(X < \sigma x + \mu) \\
  &= F(\sigma x + \mu) \\
  &= \int_{-\infty}^{\sigma x + \mu} \frac{1}{\sqrt{2 \pi \sigma^2}} e^{\frac{(t - \mu)^2}{2\sigma^2}} dt
\end{align}
Now let $s = f(t) = \frac{t - \mu}{\sigma}$, then $ds = \frac{dt}{\sigma}$ and
$f(\sigma x + \mu) = x$, so
\begin{align}
  P(\frac{X - \mu}{\sigma} < x) &= \int_{-\infty}^{x} \frac{1}{\sqrt{2 \pi}} 
    e^{\frac{s^2}{2}} ds.
\end{align}
There is no need to evaluate this integral, as we recognize it as the CDF
of a normal distribution with $\mu = 0$ and $\sigma^2 = 1$.

</div>\EndKnitrBlock{solution}

```r
set.seed(1)
# b
ggplot(data = data.frame(x = seq(-15, 15, by = 0.01)), aes(x = x)) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 1), aes(color = "sd = 1")) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 0.4), aes(color = "sd = 0.1")) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 2), aes(color = "sd = 2")) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 5), aes(color = "sd = 5"))
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-28-1.png" width="672" />

```r
# c
mean_par   <- 50
nsamps     <- 100000
pois_samps <- rpois(nsamps, lambda = mean_par)
norm_samps <- rnorm(nsamps, mean = mean_par, sd = sqrt(mean_par))
my_plot    <- ggplot() +
  geom_bar(data = tibble(x = pois_samps), aes(x = x, y = (..count..)/sum(..count..))) +
  geom_density(data = tibble(x = norm_samps), aes(x = x), color = "red")
plot(my_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-28-2.png" width="672" />


\BeginKnitrBlock{exercise}\iffalse{-91-76-111-103-105-115-116-105-99-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:logitpdf"><strong>(\#exr:logitpdf)  \iffalse (Logistic random variable) \fi{} </strong></span>A logistic random variable has CDF
\begin{equation}
  F(x) = \frac{1}{1 + e^{-\frac{x - \mu}{s}}},
\end{equation}
where $\mu$ is real and $s > 0$. The support is on the real axis. We denote
\begin{equation}
  X | \mu, s \sim \text{Logistic}(\mu, s).
\end{equation}
The distribution of the logistic random variable resembles a normal random 
variable, however it has heavier tails.

a. Find the PDF of a logistic random variable.

b. <span style="color:blue">R: Implement logistic PDF and CDF and visually
compare both for $X \sim \text{N}(0, 1)$ and $Y \sim \text{logit}(0, \sqrt{\frac{3}{\pi^2}})$. 
These distributions have the same mean and variance. Additionally, plot
the same plot on the interval [5,10], to better see the difference
in the tails. </span>

c. <span style="color:blue">R: For the distributions in b) find the 
probability $P(|X| > 4)$ and interpret the result.  </span>
</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. 
\begin{align}
  p(x) &= \frac{d}{dx} \frac{1}{1 + e^{-\frac{x - \mu}{s}}} \\
       &= \frac{- \frac{d}{dx} (1 + e^{-\frac{x - \mu}{s}})}{(1 + e{-\frac{x - \mu}{s}})^2} \\
       &= \frac{e^{-\frac{x - \mu}{s}}}{(1 + e{-\frac{x - \mu}{s}})^2}.
\end{align}

</div>\EndKnitrBlock{solution}

```r
# b
set.seed(1)
logit_pdf <- function (x, mu, s) {
  return ((exp(-(x - mu)/(s))) / (s * (1 + exp(-(x - mu)/(s)))^2))
}
nl_plot <- ggplot(data = data.frame(x = seq(-12, 12, by = 0.01)), aes(x = x)) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 2), aes(color = "normal")) +
  stat_function(fun = logit_pdf, args = list(mu = 0, s = sqrt(12/pi^2)), aes(color = "logit"))
plot(nl_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-30-1.png" width="672" />

```r
nl_plot <- ggplot(data = data.frame(x = seq(5, 10, by = 0.01)), aes(x = x)) +
  stat_function(fun = dnorm, args = list(mean = 0, sd = 2), aes(color = "normal")) +
  stat_function(fun = logit_pdf, args = list(mu = 0, s = sqrt(12/pi^2)), aes(color = "logit"))
plot(nl_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-30-2.png" width="672" />

```r
# c
logit_cdf <- function (x, mu, s) {
  return (1 / (1 + exp(-(x - mu) / s)))
}

p_logistic <- 1 - logit_cdf(4, 0, sqrt(12/pi^2)) + logit_cdf(-4, 0, sqrt(12/pi^2))
p_norm     <- 1 - pnorm(4, 0, 2) + pnorm(-4, 0, 2)
p_logistic
```

```
## [1] 0.05178347
```

```r
p_norm
```

```
## [1] 0.04550026
```

```r
# Logistic distribution has wider tails, therefore the probability of larger
# absolute values is higher.
```

## Transformations
\BeginKnitrBlock{exercise}<div class="exercise"><span class="exercise" id="exr:unnamed-chunk-31"><strong>(\#exr:unnamed-chunk-31) </strong></span>Let $X$ be a random variable that is uniformly distributed on 
$\{-2, -1, 0, 1, 2\}$. Find the PMF of $Y = X^2$.</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}
\begin{align}
  P_Y(y) = \sum_{x \in \sqrt(y)} P_X(x) = \begin{cases}
    0           & y \notin \{0,1,4\} \\
    \frac{1}{5} & y = 0 \\
    \frac{2}{5} & y \in \{1,4\}
  \end{cases}
\end{align}

</div>\EndKnitrBlock{solution}



\BeginKnitrBlock{exercise}\iffalse{-91-76-111-103-110-111-114-109-97-108-32-114-97-110-100-111-109-32-118-97-114-105-97-98-108-101-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:lognpdf"><strong>(\#exr:lognpdf)  \iffalse (Lognormal random variable) \fi{} </strong></span>A lognormal random variable is a variable whose logarithm is normally
distributed. In practice, we often encounter skewed data. Usually
using a log transformation on such data makes it more symmetric and therefore
more suitable for modeling with the normal distribution (more on why we wish 
to model data with the normal distribution in the following chapters).

a. Let $X \sim \text{N}(\mu,\sigma)$. Find the PDF of $Y: \log(Y) = X$.

b. <span style="color:blue">R: Sample from the lognormal distribution with
parameters $\mu = 5$ and $\sigma = 2$. 
Plot a histogram of the samples. 
Then log-transform the samples and plot a histogram along with the
theoretical normal PDF.</span>

</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a. 
\begin{align}
  p_Y(y) &= p_X(\log(y)) \frac{d}{dy} \log(y) \\
         &= \frac{1}{\sqrt{2 \pi \sigma^2}} e^{\frac{(\log(y) - \mu)^2}{2 \sigma^2}} \frac{1}{y} \\
         &= \frac{1}{y \sqrt{2 \pi \sigma^2}} e^{\frac{(\log(y) - \mu)^2}{2 \sigma^2}}.
\end{align}

</div>\EndKnitrBlock{solution}

```r
set.seed(1)
nsamps   <- 10000
mu       <- 0.5
sigma    <- 0.4
ln_samps <- rlnorm(nsamps, mu, sigma)
ln_plot  <- ggplot(data = data.frame(x = ln_samps), aes(x = x)) +
  geom_histogram(color = "black")
plot(ln_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-34-1.png" width="672" />

```r
norm_samps <- log(ln_samps)
n_plot     <- ggplot(data = data.frame(x = norm_samps), aes(x = x)) +
  geom_histogram(aes(y = ..density..), color = "black") +
  stat_function(fun = dnorm, args = list(mean = mu, sd = sigma), color = "red")
plot(n_plot)
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-34-2.png" width="672" />

\BeginKnitrBlock{exercise}\iffalse{-91-80-114-111-98-97-98-105-108-105-116-121-32-105-110-116-101-103-114-97-108-32-116-114-97-110-115-102-111-114-109-93-}\fi{}<div class="exercise"><span class="exercise" id="exr:unnamed-chunk-35"><strong>(\#exr:unnamed-chunk-35)  \iffalse (Probability integral transform) \fi{} </strong></span>This exercise is borrowed from Wasserman. 
Let $X$ have a continuous, strictly increasing CDF $F$. Let $Y = F(X)$.

a. Find the density of $Y$. This is called the probability integral transform.

b. Let $U \sim \text{Uniform}(0,1)$ and let $X = F^{-1}(U)$. Show that
$X \sim F$. 

c. <span style="color:blue">R: Implement a program that takes Uniform(0,1) 
random variables
and generates random variables from an exponential($\beta$) distribution.
Compare your implemented function with function __rexp__ in R.</span>

</div>\EndKnitrBlock{exercise}
\BeginKnitrBlock{solution}<div class="solution">\iffalse{} <span class="solution"><em>Solution. </em></span>  \fi{}

a.
\begin{align}
  F_Y(y) &= P(Y < y) \\
         &= P(F(X) < y) \\
         &= P(X < F_X^{-1}(y)) \\
         &= F_X(F_X^{-1}(y)) \\
         &= y.
\end{align}
From the above it follows that $p(y) = 1$. Note that we need to know the
inverse CDF to be able to apply this procedure.

b.
\begin{align}
  P(X < x) &= P(F^{-1}(U) < x) \\
           &= P(U < F(x)) \\
           &= F_U(F(x)) \\
           &= F(x).
\end{align}

</div>\EndKnitrBlock{solution}

```r
set.seed(1)
nsamps <- 10000
beta   <- 4
generate_exp <- function (n, beta) {
  tmp <- runif(n)
  X   <- qexp(tmp, beta)
  return (X)
}
df <- tibble("R"           = rexp(nsamps, beta), 
             "myGenerator" = generate_exp(nsamps, beta)) %>%
  gather()
ggplot(data = df, aes(x = value, fill = key)) +
  geom_histogram(position = "dodge")
```

<img src="04-random_variables_files/figure-html/unnamed-chunk-37-1.png" width="672" />