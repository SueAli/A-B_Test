
# A/B Test : Final Project

## Experiment Overview: Free Trial Screener

At the time of this experiment, Udacity courses currently have two options on the home page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.

In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead.  This below screenshot shows what the experiment looks like.

<img src="./Final Project- Experiment Screenshot.png"  alt="new screen">  </br>

The hypothesis was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.
The unit of diversion is a cookie, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.


## Experiment Design
### Metric Choice

Find below the available metrics that can be used in our experiment. Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.

- **Number of cookies:** That is, number of unique cookies to view the course overview page. (dmin=3000)
- **Number of user-ids:** That is, number of users who enroll in the free trial. (dmin=50)
- **Number of clicks:** That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). (dmin=240)
- **Click-through-probability:** That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. (dmin=0.01)
- **Gross conversion:** That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. (dmin= 0.01)
- **Retention:** That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. (dmin=0.01)
- **Net conversion:** That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. (dmin= 0.0075)

There are categories of metrices that would be selected before we conduct our experiment:<br/>
A. **Invariant metrics:** shouldn't change across control group and experiment group. Afetr conducting our experiment, they will be used to assure the consistency of our experiment design. The main goal of our experiment to show if the "new pop message"- that will be shown after user click on "start free trial"- might cause reducing the number of frustrated students who left the free trial because they didn't have enough time. Based on our change in UI, we should expect that any interaction between user & home page content should be the same. Which means number of page views, number of clicks on "start free trial", and number of clicks on "Access course materials" shouldn't change across control group and experiment group. So, the selected invariant metrics are: 

- Number of cookies
- Number of clicks
- Click-through-probability

B. **Evaluation metrics:** are metrics expected to change after as a result of conducting our experiment. Which change we are look for? we expect the users enrollment count/rate will change. So we should find some metrics that reflect and measure those changes. Our hypothesis expect only users with high potential - have more time to work on course - will enroll and enter their payments info. Also, because they know how much time they will need to finish the course, that increase their probabilities to stay enrolled past the 14-day boundary and make thus make at least one payment. Therefore, the selected evaluation matrices are: <br/>

- Gross conversion --> it refelects the change in enrollment rate
- Retention --> it reflect how users retention changed as a result of our experiment ( as it divides #of enrolled users who make at least one payment over # of user-ids to complete checkout). Note: some of users might cancel enrollment before they past the 14 day boundary ( those users will be counted in the denominator)
- Net conversion --> it measures if the change has any effect on  overall 14-day completion rate. 
    
If our hypothesis is true. We expect some changes in the above evaluation metrics:

- Gross Conversion to go lower, as student who likely to cancel their enrollment during 14-day trial boundary expected to be decreased by the new pop screen
- Retention would go higher, as enrolled students would not be likely to drop the course and pay at least one payment.
- Net conversion is expected to unchange. As the amount of students who past 14-day trial and make at least one payment shouldn't be affected by the change.
 
### Measuring Standard Deviation

Before conducting our experiment we should measure our metrics variability to see if we can trust our experiment design or not.
The table below shows our basline values. All counts were collected daily.

| Metric | Value|
|:---------|------:|
|Unique cookies to view page per day: |40000|
|Unique cookies to click "Start free trial" per day:|3200|
|Enrollments per day:|660|
|Click-through-probability on "Start free trial":|	0.08|
|Probability of enrolling, given click:|	0.20625|
|Probability of payment, given enroll:|0.53|
|Probability of payment, given click:|0.1093125|

Now, we want to calculate the standard deviation for our evaluation metrics, given that we have a daily sample of 5000 pageview in each group (control & experiment). 

First, let's see what data we have: our baseline values is for 40000 pageview, but our sample for each group consists of 5000 cookies only. Our baseline values should be scaled by a factor (Number of pageviews in sample/ Number of all pageviews in a day) 



```python
Scale_Factor = (5000/40000.)
print "Scale_Factor = ", Scale_Factor
```

    Scale_Factor =  0.125


Now, Calaculate an estimate for clicks and enrollment corresponded to 5000 pageviews:


```python
import math
unique_c_wt_clicks = math.ceil(3200 * Scale_Factor)
enroll_per_day = math.ceil(660 * Scale_Factor)
print '- Unique cookies to click "Start free trial" per day = ',unique_c_wt_clicks 
print '- Enrollments per day=',enroll_per_day
```

    - Unique cookies to click "Start free trial" per day =  400.0
    - Enrollments per day= 83.0


** Calculating Standard Deviation for Evaulation Metrics:**


```python
#print "- Gross conversion"
Pgc = 0.20625
Pgc_StdDev = math.sqrt((Pgc * (1-Pgc))/unique_c_wt_clicks)
#print "Pgc =", "Probability of enrolling, given click =",Pgc
print "Gross Conversion standard deviation = ",Pgc_StdDev
```

    Gross Conversion standard deviation =  0.020230604137



```python
#print "- Retention"
Pr = 0.53
Pr_StdDev = math.sqrt((Pr * (1-Pr))/enroll_per_day)
#print "Pr =", "Probability of payment, given enroll =",Pr
print "Retention standard deviation = ",Pr_StdDev
```

    Retention standard deviation =  0.0547832530914



```python
#print "- Net Conversion"
Pnc = 0.1093125
Pnc_StdDev = math.sqrt((Pnc * (1-Pnc))/unique_c_wt_clicks)
#print "Pnc =", "Probability of payment, given click =",Pnc
print "Net Conversion standard deviation = ",Pnc_StdDev
```

    Net Conversion standard deviation =  0.0156015445825


*Do you expect the analytic estimates to be accurate? That is, for which metrics, if any, would you want to collect an empirical estimate of the variability if you had time?* <br/>
The analytic estimates of standard deviation expected to be close to the empirical estimates for standard deviation for metrics where the unit of diversion is equal to the unit of analysis. This apply on metrics Net Conversion & Gross conversion (unit of analysis = unit of diversion = unique cookie) but doesn't apply on Retention metric where the unit of analysis is user_id. So,it would be better if we calculate Retention standard deviation empirically.

### Sizing
#### Number of Samples vs. Power
I will not use Bonferroni correction during my analysis phase. Using the given baseline values that provided above, we will calaculate the pageviews required for each metric. I will use this <a href="http://www.evanmiller.org/ab-testing/sample-size.html"> calculator</a> given an alpha of 0.05 and a beta of 0.2.<br/> to calaculate the sample size for each metric:

- *Gross Conversion* <br/>
    Sample size = 25,835 <br/>
    As we have two groups (control & experiment) --> total sample size = 2 *  25,835 =  51,670 enrollments <br/>
    Clicks per pageview ratio = 3,200/40,000 = 0.08 clicks/pageview<br/>
    Pageviews = 51,670 / 0.08 = 645,875<br/>
    
    
- *Retention*<br/>
    Sample size = 39,115 <br/>
    Total Sample size = 2 * 39,115 = 78,230 enrollments<br/>
    Enrollments per page view ratio = 660/40,000 = 0.0165 enrollment/pageview<br/>
    Pageviews = 78,230/0.0165 = 4,741,213<br/>
    
    
- *Net Conversion*<br/>
    Sample size = 27,413<br/>
    Total Sample size = 27,413 * 2 = 54,826 <br/>
    Clicks per pageview ratio = 3,200/40,000 = 0.08 clicks/pageview<br/>
    Pageviews = 54,826/0.08 = 685,325 <br/>
   

A sample of 4,741,213 pageview is required to conduct the experiment.

#### Duration vs. Exposure

The exposure could be specified based on the risk & safety of the experiment. I believe applying the experiment has a minimal risk on Udacity users ( small popup window will be displayed to the user and cannot block their usual interactions with Udacity). The experiment doesn't include collecting any sensitive data about the user, also no one could be physically or emotionally harmed in anyway during the experiment. Therefore, a 100% exposure is safe. 

Now, let's calculate the duration required to collect our sample data 4,741,213 pageviews. Given 40,000 pageview per day, with 100% exposure 119 day will be the duration of the experiment. I think, this is a long duration for a safe experiment. To reduce the duration, "Retention" metric can be excluded from evaluation metrics. This will reduce the total pages required to 685,275 pageview. With 100% exposures, the duration will be 18 days which is a reasonable duration to conduct the experiment. As a result, our final evaluation metrics are "Net Conversion" and "Gross Conversion" metrics. 

## Experiment Analysis
### Sanity Check

Note: here is <a href= "./ControlData.csv"? > experiment</a> and  <a href= "./ExperimentData.csv"? > control</a> groups data.
Let's do a sanity check for each invariant metric to make sure our assumptions about the experiment have been satisfied. Given 95% confidence interval for the value we expect to observe. We expect that the traffic will be divided between the control and experimental group by 50:50. 
p= 0.5 , Z= 1.96
###### Number of Cookies 


```python
import math
Cookies_StdDev = math.sqrt((0.5*(1-0.5)/(345543+344660)))
print "Standard_devitaion =",Cookies_StdDev
margin_of_error_cookies = 1.96 * Cookies_StdDev 
print "Margin of Error = 1.96 * ", Cookies_StdDev ,"= (+/-)",margin_of_error_cookies
CI_Cookies = [ 0.5 -margin_of_error_cookies, 0.5+ margin_of_error_cookies]
print "Confindence Interval = ", CI_Cookies
print "Observed value = " ,345543/float(345543+344660)
```

    Standard_devitaion = 0.000601840740294
    Margin of Error = 1.96 *  0.000601840740294 = (+/-) 0.00117960785098
    Confindence Interval =  [0.49882039214902313, 0.5011796078509769]
    Observed value =  0.500639666881


Number of cookies' observed value is within 95% confidence interval --> It is passed the sanity check.

-----------------------------
###### Number of clicks on "Start free trial"


```python
Clicks_StdDev = math.sqrt((0.5*(1-0.5)/(28378+28325)))
print "Standard_devitaion =",Clicks_StdDev
margin_of_error_clicks = 1.96 * Clicks_StdDev 
print "Margin of Error = 1.96 * ", Clicks_StdDev ,"= (+/-)",margin_of_error_clicks
CI_Clicks = [ 0.5 -margin_of_error_clicks, 0.5+ margin_of_error_clicks]
print "Confindence Interval = ", CI_Clicks
print "Observed value = " , 28378/float(28378+28325)
```

    Standard_devitaion = 0.0020997470797
    Margin of Error = 1.96 *  0.0020997470797 = (+/-) 0.00411550427621
    Confindence Interval =  [0.49588449572378945, 0.5041155042762105]
    Observed value =  0.500467347407


Number of cookies' observed value is within 95% confidence interval --> It is passed the sanity check.

-------
###### Click-through-probability on "Start free trial"


```python
CTR_Control = 28378/float(345543)
print "Click-through-Rate for Control group = ", CTR_Control
CTR_Control_STD = math.sqrt((CTR_Control*(1-CTR_Control)/345543))
print "Standard_devitaion = ", CTR_Control_STD
ME_CTR_Control = 1.96 * CTR_Control_STD
print "Margin of error = (+/-)", ME_CTR_Control
print "Confidence Interval = ",[CTR_Control - ME_CTR_Control, CTR_Control + ME_CTR_Control]
print "Observed Value = ", 28325/float(344660)
```

    Click-through-Rate for Control group =  0.0821258135746
    Standard_devitaion =  0.000467068276555
    Margin of error = (+/-) 0.000915453822047
    Confidence Interval =  [0.08121035975252971, 0.08304126739662393]
    Observed Value =  0.0821824406662


Click-through-probability pass the sanity check. <br/>
             *<b> All selected invariant metrics had passed the sanity check. </b>*

### Results Analysis
#### Effect Size Tests
Given 95% Confidence interval for the difference between the experiment and control group for evaluation metrics.

1 - Calculate p & se for the pool: <br/>
    p = (X_cnt + X_exp)/ (N_cnt + N_exp)  <br/>
    se = sqrt(p*(1-p)*(1/N_cnt + 1/N_exp)) <br/>
2 - Calculate the difference  <br/>
    d = (X_exp/N_exp) - (X_cont/N_cont) <br/>
3 - Calculate the Confidence Interval  <br/>
    CI = [ d - se , d + se ] <br/>

**Gross conversion:** <br/>
 N_cnt = 17293 , X_cnt = 3785, N_exp= 17260, and X_exp= 3423 <br/>


```python
N_cnt = 17293. 
X_cnt = 3785.
N_exp= 17260.
X_exp= 3423.
gc_p_pool = (X_cnt + X_exp) / (N_cnt + N_exp)
gc_se_pool = math.sqrt((gc_p_pool*(1-gc_p_pool)*((1/N_cnt)+(1/N_exp))))
gc_me = 1.96* gc_se_pool
gc_d = (X_exp/N_exp) - (X_cnt/N_cnt)
gc_CI = [gc_d - gc_me, gc_d+gc_me]
print "GrossConversion_P_Pool = ",gc_p_pool
print "GrossConversion_Margin_of_Error = ", gc_me
print "GrossConversion_Diff = ", gc_d
print "GrossConversion_CI = ",gc_CI
```

    GrossConversion_P_Pool =  0.208607067404
    GrossConversion_Margin_of_Error =  0.00856848375504
    GrossConversion_Diff =  -0.0205548745804
    GrossConversion_CI =  [-0.0291233583354044, -0.01198639082531873]


*The condience interval does not include Zero, that means the metric is statisitcally significant. Also, It does not inculde dmin ( = (-)0.01) which means it is a practically significant.*

**Net conversion:** <br/>
N_cnt = 17293, X_cnt = 2033 ,N_exp = 17260 , and X_exp =1945<br/>


```python
N_cnt = 17293.
X_cnt = 2033.
N_exp = 17260.
X_exp =1945.

nc_p_pool = (X_cnt + X_exp) / (N_cnt + N_exp)
nc_se = math.sqrt(nc_p_pool * (1-nc_p_pool) * (1./N_cnt + 1./N_exp))
nc_me = 1.96 * nc_se
nc_d = (X_exp / N_exp) - (X_cnt / N_cnt) 
nc_CI = [nc_d - nc_me, nc_d + nc_me]
print "NetConversion_P_Pool = ",nc_p_pool
print "NetConversion_Margin_of_Error = ", nc_me
print "NetConversion_Diff = ", nc_d
print "NetConversion_CI = ",nc_CI

```

    NetConversion_P_Pool =  0.115127485312
    NetConversion_Margin_of_Error =  0.00673090168535
    NetConversion_Diff =  -0.00487372267454
    NetConversion_CI =  [-0.011604624359891718, 0.001857179010803383]


*The confidence interval does include Zero, that means the metric is not statistically significant. The confidence interval includes the lower boundary (-0.0075) for practical significant, that means the metric is not practically significant.* To double check our results, let's do the sign test.


#### Sign Test
To double check our evaluation metrics, let's do the sign tests. Conducting a binomial sign test ,where: 
- Each day of the experiment is evaluated to see if there is a positive or negative change/difference between the metric value across control & experiment groups.
- Each positive difference will be counted as a success.
- Each negative difference will be counted as a failure.
- Calculating the two-tailed p-value for the difference to determine if the metric is statistically significant or not. <a href="./SignTest.csv"> Sign test calculations <a> of success count & then you can use this calculator to get the corresponded p-value.


For Gross Conversion metric:
- p_value = 0.0026 
- Statistically significant: Yes (the two-tail p_value is too smaller than our alpha 0.05)
- Rate of Success = 4/23 

For Net Conversion metric:
- p_value = 0.6776 
- Staistically significant: No (the two-tail p_values is greate than alpha(0.05) )
- Rate of Success = 10/23



#### Summary

Udacity conducted an experiment to see the effect of studens knowing how much time they need in advance before they enroll themselves in any course. Udacity added a new screen "Ask student how much time they have available to work in the course" based on their answer, they will be advised to access course free material, or continue to the enrollment process directly. Udacity student's traffic was diverted equally between two groups: control group (normal UI), and experiment group ( where the new screen was applied). Some invariant metrics were selected to make sure our experiment doesn't have any undesirable changes. Invariant metrics are "Number of unique cookies", "Number of clicks on "Start free trial", and "Click-through Probability". None of them should be different across control and experiment groups.

Evaluation metrics were chosen to evaluate the impact of the experiment, and see if it meets our expectations or not. Evaluation metrics were: "Gross Conversion" and "Net Conversion" rates. To statistically evaluate the metrics, the Null hypothesis is there is no change in evaluation metrics across control & experiment groups. To decide launching the experiment, we expect this Null hypothesis to be rejected for both metrics.

The effect size test showed that Gross Conversion evaluation metric is statistically and practically significant. It also showed that Net Conversion metric is not statistically or practically significant. The Gross Conversion has been dropped by 2% in our experiment which approve the new screen has the expected impact in reducing students enrolled without having in front info about time they need to complete the course. But, for "Net Conversion" metric "Null hypothesis" was accepted and this is the opposite for what we expected.
Both effect size tests and sign test approved that "Gross Conversion" metric is a practically and statistically significant, while "Net Conversion" is neither statistically or practically significant.

To launch our experiment ALL metrics - Gross conversion and Net conversion - should be satisfied and meet the required significance, that is the main reason of not using Bonferroni correction in our analysis. To use Bonferroni correction, Null hypothesis should not require the significance of ALL metrics and that is not the case in our experiment.


### Recommendation
Our experiment goal can be divided into two main points: <br/>
1- Reducing the enrolled students who will not be committed to finish the course.  <br/>
2- Having no change or increase in the amount of students who past 14-days trial and make a payment.  <br/>
The experiment showed a statistically and practically decrease in "Gross Conversion" which is aligned with the 1st point of experiment goal. But it also showed a decrease in "Net Conversion" which is the opposite of 2nd point in the experiment goal, so it is not aligned with the experiment goal. The amount of students who past 14-days trial and make a payment shouldn't be affected by the experiment. Based on these results, my recommendation is not to launch this experiment.

## Follow-Up Experiment

In previous experiment, it was assumed that wrong time estimation in prior to enrollment was the main reason for student to cancel their enrollment before they past 14-day trial. I believe there are many other reasons that might cause students with high commitment and enough time cancel their enrollment before they past 14-days trial. Some students might leave because it was the first time for them to take an online course and they didn't feel comfortable with online learning process, the course content wasn't what they expected, they are missing the pre-requisite skills, or they discovered they should pay less for the course.

If we assumed that students could be more motivated to pass the 14-days trial, if they offered a discount on the second month of enrollment Find below my recommendation for this experiment:

- Null Hypothesis: Adding the question "Would you like to have a discount on next month enrollment fee?" will not increase the number of students who past the 14-day free trial period by a significant amount
- Unit of Diversion: The unit of diversion will be a user id as the change of the proposed change take place after student enrollment
- Invariant Metrics: Count of user_ids as they shouldn't be affected by the change.
- Evaluation Metrics: Retention metric could be used as it should be increased if our intention is true.
If a significant change was found, I will recommend launching the experiment.


