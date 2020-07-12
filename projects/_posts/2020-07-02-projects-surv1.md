---
layout: post
title: Survival Analysis basics
categories: [projects, p1]
description: >
  Survival analysis basics. This posts will elaborate censoring, hazard function and nonparametric survival analysis(KM estimator)
sitemap: false
---

When you are interested in the 'time' to event, Survival analysis will be the first word that pops up in your head. As its name implies, you will be calculating the 'survival time' to the events no matter what your event is: it can be death(typically), exit from certain cluster, malfunction, etc.  You should not think of OLS, for the time to event does not follow normal distribution in the first place and  OLS can not tackle the *censored* data well, which will definitely appear in your data set.

In this post, several basic but essential concepts will be described. And then the nonparametric and parametric models especially, the most dominant model :Cox proportional hazards regression model, and its extensions will follow. 


# Time to event and Censoring

When you are interested in the *time to event(duration)* as well as the event itself, you want to draw the distribution of the times. However, in most times you will never know the exact distribution. If the **censoring** happens, which is the incomplete observation, there is no way for you to know the truth. And here the need for the Survival analysis arises. 

**Censoring** means the incomplete observation of *failure time*(the time when the event happens). It can happen due to administrative reasons or simply because it is unavailable to observe. Here I'll leave a short description on only the directions of censoring for the sake of simplicity. 

* RIGHT censoring
	The time flows in one direction (from past to future). Put the past on the left and the other on the right. Right censoring means the someone is censored after some observations(after some time passed). If someone withdraws the study or is lost during the follow-up, he/she is (right) censored. If someone does not experiences the event till the very end of the study, he/she is also (right) censored : he/she will eventually experience it in the future(right side) but we cannot observe it for now. 
* LEFT censoring
	In some studies(mostly in the medical field), a certain time before the actual observation might be given after registration. If someone is censored during the period between those two, he/she is (left) censored as the censoring occurs even before the observation (on the left side)! 
	
You might not overlook this **censoring** because there is no simple way to tackle it. If you ignore it and just treat censoring as the event you will *underestimate* the true mean time. And if you delete all the data points that are censored, you will lost the information (which can be fatal in some cases). 

Survival Analysis has been studied to find the proper way to estimate the failure time distribution even when the censoring happens.  Before jumping on the actual models in Survival Analysis, lets recap the important concepts first. 

# Hazard function
Indeed, all you have to know is what the *hazard* is for the rest of the discourse. To get the idea of what it is, we should define the random variable and its $F(t)$ and the survival function $S(t)$ first.

### random variable $T_i$ and  distribution $F(t)$
Our random variable $T_i$ is the time to event that observe, which can be both continuous and discrete. It is obviously non-negative and its distribution is defined as below. ($t$ is a certain time point)$$F(t) = P(T_i<t) = \ \int_0^tf(s)ds$$$F(t)$ is continous and differentiable (the $T_i$ is continous for now). Even if it's not, there is also a way to find $f(t) = F'(t) = dF(t)/dt$ anyway (*Stieltjes integration*). 
$$f(t)=lim_{dt \rightarrow 0}\frac{1}{dt}P(t\leq T_i < t+dt)$$
$f(t)$ is the limiting quantity that describes the likelihood of $T_i$ to fall in the very short interval after time $t$. To simply put, it is *the probability of event happens almost right after time t* 

### Survival Function $S(t)$
Then what should we know? We are interested in *how long the subject survives*. Therefore, we should look up the probability of the event happens longer than time $t$! $$S(t)=P(T_i>t)=1-F(t)$$ As the $F(t)$ is an increasing function from 0, $S(t)$ decreases from 1: that is the survival probability of subject $i$ falls over time. If $S(\inf)=0$ we say it is *proper* survival function. If somebody is cured it will never reach 0 even after forever.  

### Hazard Function $\lambda(t)$
Then you might want to see the *how dangerous a subject is* at each time: the RISK.  So you want to narrow the focus of failure time only for those who already survived, which leads ous to the hazard function!
$$\lambda(t) = lim_{dt\rightarrow0^+}\frac{1}{dt}P(t\leq T_i < t+dt|T_i\geq t)=\frac{f(t)}{S(t)}$$ It is a **limiting conditional rate**  that that measures the rate of failure in $[t,t+dt)$ when the failure has not occured at time $t$. It is **instantaneous** rate as the $dt$ is really short! 

The hazard function has several different names : Hazard rate, Conditional failure rate, the intensity rate. Why ***rate***? You may think it looks like a density function, however, the hazard rate is never a probability if the $T_i$ is *continuous*; as it is a probability divided by $dt$ + it could be >1. (elaboration is needed Kangki!!) However, if the $T_i$ is discrete, it is a probability.

Cumulative hazard $\Lambda(t)$ is used for the convenience. As the hazard itself can not be monotonic as $F(t)$ or $S(t)$, calculating it directly can be very complicating. So we sum up!$$\Lambda(t)=\int_o^t \lambda(s)ds$$ $\Lambda(t)$ measures the total amount of risk that has been accumulated up to time $t$.  

I'd fancy give you a intuition on each functions, however, $\Lambda(t)$ is more like a tool for the sake of mathematical convenience. As you all recognised all functions ($F(t), f(t), S(t), \lambda(t)$) can be linked/expressed if you define only one of it. If you introduce $\Lambda(t)$ in that procedure, it will be much more convenient. 

IF the data is count data, $\Lambda(t)$ gives a expected  the number of failure times over a given period, if only the failure event were repeatable:  the expected number of deaths of an individual up to time $t$ if the individual were to be resurrected after each death (without resetting time to zero). I'd leave a great comment that I can relate on this

> I would emphasize that the cumulative hazard function does not have a nice interpretation, and as such I would not try to use it as a way to interpret results; telling a non-statistical researcher that the cumulative hazards are different will most likely result in an "mm-hm" answer and then they'll never ask about the subject again, and not in a good way. 
> [https://stats.stackexchange.com/questions/60238/intuition-for-cumulative-hazard-function-survival-analysis](https://stats.stackexchange.com/questions/60238/intuition-for-cumulative-hazard-function-survival-analysis)

# Kaplan-Meier estimator
If you get the above concepts, you can move on to calculate those probabilities/rates now. Kaplan-Meier estimator is a famous nonparametric estimator in Survival Analysis

## Getting started
* [Install]{:.heading.flip-title} --- How to install and run Hydejack.
[install]: README.md
