---
layout: page
permalink: /blogs/Statistics/Probability/index.html
title: Probability
---

# A/B Testing



## 1. Overview

### 1.1 What is A/B Testing?

A general methodology used online when you wanna test out a new product or a feature. Use two sets of users: control set (existing product) and experiment set (new version) by obsevering how these users respond differently to determine which version of feature/product is better.

### 1.2 Pros & Cons

- **useful:** 1) help you climb to the peak of your current mountain

- **not useful:** 1) test new experiences; old users may resist against the new version (change aversion), or old users may all go for the new experience, then test everything (novelty effect). Two issues to consider when it comes to a new experience: (1) what is your baseline of comparison? (2) how much time do you need in order for your users to adapt to the new experience, so that you can actually say what is the plateaued experience and make a robust decision? 

  2) long term effect is hard to test too, such as a website that recommends apartment rentals; note update brand or logo can't use ab testing, it's surprisingly emotional and users need some time to get used to new logo 

  3) can't really tell you if you're missing something

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

**Assumption:** 

- population is large enough to invoke the CLT (**To use normal**): N > 25,  $N\ *\ \hat{p}$ > 5 **and** $N\ (1- \hat{p})$ > 5

- Expection probability:  $\hat{p} = \frac{X}{N}$
- Standard deviation: $\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$
- margin of error = $Z \ *\ \sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$
- Z = $\frac{\hat{p}\ -\ p}{\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}}$



**Confidence interval =  $\hat{p}$ +-  ME (margin of error)**

**Practical significant: refers to the real-world importance or meaningfulness of an effect or result.**

比如通过考试成绩来判断study program的有效性，我们把5%认定为是有practical significant (i.e. an efffect of 4 points or less is too small to care about). 在进行了实验后，我们发现两组有statistically significant difference，参加study program的人得分比没参加的多了3%（百分制）。然而根据先前定的practical significant  来看，3%是小于5%的，所以即使我们的实验证明了study program确实能提高分数，但在现实情况下，参与者在study program上所花的时间和金钱不值得只提高3%。



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

