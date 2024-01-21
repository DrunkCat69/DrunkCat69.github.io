---
layout: page
permalink: /blogs/Statistics/AB Testing/index.html
title: AB Testing
---

# A/B Testing



## 1. Overview

### 1.1 What is A/B Testing?

A general methodology used online when you wanna test out a new product or a feature. Use two sets of users: control set (existing product) and experiment set (new version) by obsevering how these users respond differently to determine which version of feature/product is better.

### 1.2 Pros & Cons

- **useful:** 1) help you climb to the peak of your current mountain
- **not useful:** 
- test new experiences; old users may resist against the new version (change aversion), or old users may all go for the new experience, then test everything (novelty effect). Two issues to consider when it comes to a new experience: (1) what is your baseline of comparison? (2) how much time do you need in order for your users to adapt to the new experience, so that you can actually say what is the plateaued experience and make a robust decision? 
  - long term effect is hard to test too, such as a website that recommends apartment rentals; note update brand or logo can't use ab testing, it's surprisingly emotional and users need some time to get used to new logo
- can't really tell you if you're missing something

### 1.3 Choose a metric

$$
click\ through\ rate = \frac{number\ of\ clicks}{number\ of\ page\ views}
$$

$$
click\ through\ probability = \frac{unique\ vistors\ who\ clicks}{unique\ vistors\ to\ page}
$$

#### **Binomial Distribution**

1. 2 Types of outcomes (success/failure)
2. The probability of success (p) stays the same from one trial to another. The probability of failure (q) = 1 – p
3. All trials are **independent**
4. Identical distribution (i.e p same for all)



#### One sample Z test about p

- **Assumption:** 
  - population is large enough to invoke the CLT (**To use normal**): N > 25,  $N\ *\ \hat{p}$ > 5 **and** $N\ (1- \hat{p})$ > 5
  - Expection probability:  $\hat{p} = \frac{X}{N}$
  - Standard deviation: $\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$
  - margin of error = $Z \ *\ \sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$
  - Z = $\frac{\hat{p}\ -\ p}{\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}}$


- **Confidence interval =  $\hat{p}$ +-  ME (margin of error)**

- **Practical significant: refers to the real-world importance or meaningfulness of an effect or result.**
  - 比如通过考试成绩来判断study program的有效性，我们把5%认定为是有practical significant (i.e. an efffect of 4 points or less is too small to care about). 在进行了实验后，我们发现两组有statistically significant difference，参加study program的人得分比没参加的多了3%（百分制）。然而根据先前定的practical significant  来看，3%是小于5%的，所以即使我们的实验证明了study program确实能提高分数，但在现实情况下，参与者在study program上所花的时间和金钱不值得只提高3%。



#### Size V.S. power trade-off

Statistical power helps us find how many samples do we need to have statistical significance.

$\alpha$ = P(reject null  |  null TRUE) 拒绝了原本对的

$\beta$ = P(fail to reject  |  null FALSE) 接受了本该错的

sensitivity = 1 - $\beta$, often 80%



**Small sample: $\alpha$ low, $\beta$ high**

![small sample](https://drunkcat69.github.io/images/AB Test/small sample.png)



**Large sample: $\alpha$ same, $\beta$ low**

![large sample](https://drunkcat69.github.io/images/AB Test/large sample.png)

#### confidence interval case breakdown

![CI cases](https://drunkcat69.github.io/images/AB Test/CI cases.png)

- case2: neutral change
- case4: the confidence interval bounds are outside of what's practically significant, it could be causing them to increase or decrease
- case5: confidence interval overlaps 0, there might not even be a change at all
- case6: it's possible your change is not practically significant



## 2. Choosing and Characterizing Metrics

Types of Metrics:

1. Sanity checking metrics (Invariant Metrics) — can be multiple metrics. Eg. When changing the page and testing CTR, latency & load times should be invariant
2. Evaluation metrics — one or multiple.

For multiple metrics, composite metrics and OEC (overall evaluation criterion), they are hard to define (it need to agree on a definition/end up looking all the individual metrics).

### Metric Definition

1. come up with a high-level concept for a metric, such as "active users", which can be understood by everyone.
2. details: how do you define "active";
3. summarize all of these individual data measurements into a single metric such as count or sum;

### Refining the customer funnel (Udacity example)

- Homepage visits
- Exploring the site
  - Users who view course list
  - Users who view course details
- Create an account
  - Users who enroll in a course
  - Users who finish lesson 1，2，etc.
  - Users who sign up for coaching at various levels
- Completeing a course
  - Users who enroll in a second class
  - Users who gets jobs

![customer funnel](https://drunkcat69.github.io/images/AB Test/customer funnel.png)

### Metric Choosing

![metric choosing](https://drunkcat69.github.io/images/AB Test/metric choosing.png)

### Difficult metrics

- Don't have access to data
- takes too long

### Technique to deal with difficulty

1. **External Data**: Use **industry metrics** from external data (Neilson, comscore), **Survey data** (Pew, Forrester), **Academic research**.
2. **Internal Existing Data**: Retrospective analysis (correlation but not causation), running experiments, user experience research, surveys, and focus groups.

**Retrospective analysis:** used to look at how a metric changes in response to past changes or experiments. This can help establish a baseline and develop theories.

### Techniques to Gather Additional Data

- User Experience Research (UER)

  +good for brainstorming

  +can use special equipment (catch eye movement)

  -want to validate results

- Focus Groups

  +get feedback on hypotheticals

  -Run the risk of group think

- Surveys

  +Useful for metrics you cannot directly measure (whether student who take course get job)

  -Can't directly compare to other results (more observational methods)

## Metric definitions

High-level metric: click through probability:  $\frac{users\ who\ click}{users\ who\ visit}$

- Def #1 (Cookie probability): For each time interval, $\frac{number\ of\ cookies\ that\ click} {number\ of\ cookies}$, for users who **clicks the back button and returns to a previously visited page**, the browser may **retrieve the cached version of that page** instead of making a new request to the server. 即返回前一页, present cookie仍存在。

- Def #2 (Pageview probability): $\frac{Number\ of\ pageviews\ with\ a\ click\ within\ time\ interval}{number\ of\ pageviews}$


- Def #3 (Rate): $\frac{Number\ of\ clicks}{Number\ of\ pageviews}$


### Summary Metrics

Establish a few characteristics for your metric:

1. Sensitivity and robustness
2. Characterize the distribution: retrospective analysis

Four categories of metrics to keep in mind:

- sums and counts
- Distributional metrics: means, median, and percentiles (75th, 90th)
- Probabilities and rates
- Ratios

**Sensitivity:** how well a metric can detect changes that you care about

**Robustness:**  the metric's ability to remain stable when there are changes that you don't care about, it doesn't move a lot when nothing interesting has happened.

**2 WAYS to Measure:**

1. Running experiments, a/a experiment to determine if they're too sensitive.
2. **Retrospective analysis of your logs (robustness).** Involving looking back at past experiments or changes made to the website and examining if the metric values moved in conjunction with those changes (if remain stable or show minimal change, indicate robust). 
3. Analyze the history of the metric and try to find causes for any major changes observed **(hindsight)**. It can provide valuable insight into the behavior of the metric over time and help you understand its robustness.



### Absolute vs. relative difference

Suppose you run an experiment where you measure the number of visits to your homepage, and you measure 5000 visits in the control and 7000 in the experiment. Then the absolute difference is the result of subtracting one from the other, that is, 2000. The relative difference is the absolute difference divided by the control metric, that is, 40% ($\frac{2000}{5000} * 100\%$).



### Variability

| Type of Metric    | Distribution          | Estimated Variance                    |
| ----------------- | --------------------- | ------------------------------------- |
| Probability       | binomial **(normal)** | $\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$ |
| mean              | normal                | $\frac{\sigma^2}{N}$                  |
| median/percentile | depends<sup> 1</sup>  | depends                               |
| count/difference  | normal **(maybe)**    | Var(X) + Var(Y)                       |
| rates             | poisson<sup>2</sup>   | $\bar{X}$                             |
| ratios            | depends<sup>3</sup>   | depends                               |

1. median - could be non-normal if data is non-normal

   ![variability](https://drunkcat69.github.io/images/AB Test/variability.png)

2. **extreme version of normal distribution, N super large, p super small.** 泊松分布是指某段**连续的时间内**某件事情发生的次数，而且“某件事情”发生所用的时间是可以忽略的。例如，在五分钟内，电子元件遭受脉冲的次数，就服从于泊松分布。假如你把“连续的时间”分割成无数小份，那么每个小份之间都是相互独立的。在每个很小的时间区间内，电子元件都有可能“遭受到脉冲”或者“没有遭受到脉冲”，这就可以被认为是一个p很小的二项分布。而因为“连续的时间”被分割成无穷多份，因此n(试验次数)很大。所以，泊松分布可以认为是二项分布的一种极限形式。

3. different ratio use different data, so it depend on the distribution for the numerator and the denominator of the ratio.



### Empirical Variability

For complicated metrics, the distribution can be weird. You might want to shift to an empirical estimate.

**A/A testing:** control A against another control A, and there’s actually **no change in what the users are seeing.** What that means is that any differences that you measure are due to the underlying variability, maybe of your system or the user population, what users are doing, all of those types of things.

Pros: If your experiment system is itself complicated, it’s actually a good test of your system.

Bootstrap: 将一个大样本按照有放回的取样方法，取出一定数量的新样本，把这些random subset当作simulated experiment. In reality, what we have is a whole gradation of different methods. If the bootstrap estimate is agreeing with your analytical estimate, you can probably move on. If not, consider big A/A testing.

![bootstrap](https://drunkcat69.github.io/images/AB Test/bootstrap.png)

### Uses of A/A Test

- Compare results to what you expect (sanity check). [Example](https://docs.google.com/spreadsheets/d/1ZIyIy23VVDVU_86uK_5RfMQyhOq0ORcoy9gQr2hTmsI/edit#gid=0)
- Estimate variance and calculate confidence
- Directly estimate confidence interval

![directly](https://drunkcat69.github.io/images/AB Test/directly.png)