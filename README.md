# Personalized-Promotions
**Keywords**: Heterogeneous Treatment Effects (HTE), Targeting, Policy Evaluation and Learning

## Summary
I am working with a real experimental [dataset](https://ods.ai/competitions/x5-retailhero-uplift-modeling/data) provided by X5 Group, a russian food [retailer](https://www.x5.ru/en/). In the experiment, the retailer chose a subgroup of the customer base and sent to half of it an SMS prompting them to perform some action ($T=1$, treatment group). The other half did not receive any intervention ($T=0$, control group). The experiment was fully randomized, meaning that each customer had a 50% chance of being assigned to the treatment group ($e = 0.5$), regardless of their behavioral or demographic information. The response $Y$ captures whether a customer performed that action that was mentioned in the SMS and is binary, $Y \in$ $`\{0,1\}`$ . The goal of the analysis is to identify the customer profiles that are responsive enough to make the intervention profitable. The customer profiles are summarised by a covariates vector $X$.



---

## Data

The datasets that we load are:
- $\text{X}$: the customer features ,
- $\text{T}$: the treatment indicator showing whether a customer received the sms or not ,
- $\text{Y}$ and the response indicator showing whether a customer performed the action under study or not 

---

## Methods

I train multiple $\text{CATE}$ estimators, including **S-Learners**,**T-Learners**, **X-Learners**, **DR-Learners**, **R-Learners**,  **Double Machine Learning** models,  **Causal Forests**, as well as regression using the **Transformed Outcome** (*an unbiased estimator of the treatment effect when* $e(x)$ *is known*), and then train and compare targeting policies based on the estimated heterogeneous treatment effects $\hat{\tau}(x)$. For policy learning I rely on two methods.

1. **First targeting policy**: Treat if $\hat{\tau}(x) \geq \frac{C}{R}$ where $C$ is the cost of treating a sample and $R$ the incremental revenue generated by an additional conversion.

   See also Hitsch, Guenter J. and Misra, Sanjog and Zhang, Walter, Heterogeneous Treatment Effects and Optimal Targeting Policy Evaluation (https://ssrn.com/abstract=3111957)

  
2. **Second targeting policy**: I train a weighted classification model using the covariates $X$ and the label $Y'$, where $Y' =1$ if $\hat{\tau}(x) \geq \frac{C}{R}$ and $0$ otherwise. The weights are proportional to the magnitude of the treatment effect. In particular the weights are calculated based on the expected profit of treating a sample with covariates $X$, $R\cdot \hat{\tau}(x) -C$. The higher this number is, the higher the expected profits/ROI of the promotions for that particular profile. It costs us more missclassifying this sample, than missclassifying another sample with a lower positive value and the second targeting policy takes that into account, whereas the first does not.  

   See also Chapter 15.4 from Chernozhukov, V. & Hansen, C. & Kallus, N. & Spindler, M. & Syrgkanis, V. (2024): Applied Causal Inference Powered by ML and AI (https://causalml-book.org)  
   

  
For (offline) policy evaluation I rely on the **Inverse Propensity Scoring** method:

$$\text{Profits}(\pi) = \sum_{i=1}^n \left ( \frac{1-T_i}{1-e(x_i)} (1 - \pi(x_i)) \cdot RY_i + \frac{T_i}{e(x_i)}\pi(x_i) \cdot (RY_i - C) \right ),$$

where $\pi(x)$, is a policy that maps covariate vectos $X$ to $`\{0,1\}`$

*Note*: If $e(x)$ was not known, we would have to rely on doubly-robust scores $\Gamma_i$ as outlined by Athey and Wager in https://arxiv.org/abs/1702.02896  



To estimate the uncertainity around policy performance, I rely on boostrapping, whereby in each iteration $t$, I randomly split the original dataset into $X_{\text{train}}$ and $X_{\text{test}}$ and then  create bootstrapped versions of them $X_{\text{B-train}}$ and $X_{\text{B-test}}$ and work with those. The reason for this split is that in that way, I make sure that none of the samples in the test set is also present in the train set. For the simulations, I conduct 50 trials for 4 different $\frac{C}{R}$ thresholds, namely $\[ 0.033, 0.04, 0.05, 0.0667 \]$, all of which are above the $\text{ATE}$ achieved by the experiment.

---


## Main Results
The Average Treatment Effect $\text{ATE}$ of the experiment is at 3.12%. For the promotion to be profitable when targeting all the customer base as it is, the cost-to-revenue ratio $\frac{C}{R}$ needs to be below 3.12% (or 0.0312). If it is above, then targeting needs to be implemented in order for the promotion to have a positive ROI. Moreover, the theoreticaly optimal solution in terms of expected profit maximization would be target any sample with $\tau(x) \geq 0.0312$. Below, I show the average profits scaled on the size of the original dataset as obtained by the best 5 $\text{CATE}$ methods after running 50 bootstrap iterations for the four different thresholds $\[ 0.033, 0.04, 0.05, 0.0667 \]$


![aggregate_performance](/figures/aggregate_performance.png)

We see that on average the **S-Learner** coupled with the **second policy** learning method produces the highest average profits over the status quo of treating noone. Treating noone is more profitable than treating everyone in these cases as the cost-to-reward ratios under study are higher than the $\text{ATE}$. We also notice that as the cost-to-reward ratio increases, the average profits decrease and because the standard deviation remains high, a higher proportion of the time does targeting result in losses. In essence, this shows that the models cannot effectively identify samples with the required uplift for profitability. Of course, this intervention is rather simple (sms), hence we do not anticipate necessarily that it will be particularly effective for any of the sample profiles.


   


Even though the S-Learner performs better on average, we can see below that in many of the iterations, other models gave the best profits. This of course hints at the value of conducting causal-stacking which is to implemented in the near future. Each inner quarter circle represents the 50 iterations for a specific cost-to-reward ratio. The middle circle shows which methods performed best and the outer shows which policy learning approach gave the best profits. It quickly becomes clear that causal forests and the s-learner are the two superior $CATE$ estimators for this dataset.

![modelperformance](/figures/model_performance.png)

We see that the causal forests model performs well a lot of th time




