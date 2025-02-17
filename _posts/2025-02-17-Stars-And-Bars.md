---
layout: post
title: Starts and Bars
categories: [notes]
tags: [ML]
---

While checking on the generalization of [HWE](https://fanhuan.github.io/en/2024/12/02/HWE/) for more than two alleles, I had to refresh my self on multinomial expansion. For any positive integer m and any non-negative integer n, the multinomial theorem describes how a sum with __m__ terms expands when raised to the __nth__ power. I do remember that the sum of exponents in each term needs to be the same as the original nth power, but I forgot on how to calculate the coefficient. 

Then I came across this method called __stars and bars__, which is used to determing how many terms an multinomial expansion has. I don't remember when or whether I've learnt this method in school, but in Chinese it is called “隔板法”（https://zh.wikipedia.org/zh-sg/%E9%9A%94%E6%9D%BF%E6%B3%95）. It is solving for the number of combinations of nonnegative integer indices `k1` through `km` such that the sum of all `ki` is `n`. Let's consider the case where we have 3 terms, a, b and c, and we want to expand to the power of 4, (a+b+c)^4. So in this case, n=4 and m=3, where we need to split 4 stars into 3 groups, with 0-4 starts in each group. How? We only need 3-1=2 bars to put amongst those starts, and they will be separated into 3 groups. 

It could be something like:

**|**| (a^2 * b^2 * c^0)

or

|*|*** (a^0 * b^1 * c^3)

As you can see the number of combination would be n + (m-1) choose (m-1), i.e, there are altogether n+m-1 positions, and we need to choose (m-1) to place the bars, simple. In our example, it would be 6 choose 2, which is 6!/(4! * 2!) = 15, and there are indeed 15 different combinations such as a^4 or b * c^3.


A harder question would be how to understand the multinomial coefficients. Let's keep thinking along the lines of stars and bars. 

When we were thinking about the number of terms, we were thinking about where to put the bars, but treating the stars anonymously (they are just stars!). Now imaging that they are not. We actually have 4 distinctive starts, 1,2,3,4, then for the first example,

**|**| (a^2 * b^2 * c^0)

there would be 6 different groupings:

12|34|
13|24|
14|23|
23|14|
24|13|

meaning, the arrangement within each group should be cancled/devided, therefore the formular is 4!/(2! * 2! * 0!), or in general term, it would be n!/(k1!k2!...km!) for each term. 

Now let's think about a special case where m = 2, meaning there are always just 2-1=1 bar, the bar can be placed at n+1 different positions, when it is placed at the kth position (let the bar be in front all the stars as 0), the coefficient would be n!/(k! * (n-k)!), which is actually $\binom{n}{k}$, the binomial coefficient 



I hope this post helps you to understand the multinomial expansion. 




