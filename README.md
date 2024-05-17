# Personalized-Promotions
**Keywords**: Heterogeneous Treatment Effects (HTE), Targeting, Policy Evaluation and Learning

### Summary:
I am working with a real experimental dataset provided by X5 Group, a russian food retailer \href[https://www.x5.ru/en/]. In the experiment, the retailer chose a subgroup of the customer base and sent to half of it an SMS prompting them to perform some action. The other half did not receive any intervention ($T=0$, control group). The experiment was fully randomized, meaning that each customer had a 50% chance of being assigned to the treatment group ($e = 0.5$), regardless of their behavioral or demographic information. The response $Y$ captures whether a customer performed that action that was mentioned in the SMS and is binary ($Y \in \{ 0,1 \}$). The goal of the analysis is to identify the customer profiles that are responsive enough to make the intervention profitable. The customer profiles are summarised by a covariates vector $X$.


### Data:

### Methods
I train multiple $\text{CATE}$ estimators, including *S-Learners*,*T-Learners*, *X-Learners*, *DR-Learners*, *R-Learners*  *Double Machine Learning*, *Causal Forests* and train targeting policies based on the estimated heterogeneous treatment effects $\tau(x)$. For policy learning I rely on two methods.
1. First targeting policy: 


### Main Results:
The Average Treatment Effect $\text{ATE}$ of the experiment is at 3.12% percent. This uplift 


