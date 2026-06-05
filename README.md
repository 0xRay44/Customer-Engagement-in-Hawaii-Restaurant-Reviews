# When Customers Complain: Customer Engagement in Hawaii Restaurant Reviews

by Rayaan Ahmed (rsahmed@ucsd.edu)


---

## Introduction

The dataset used in this project is the Hawaii Google Maps Reviews dataset, which contains information about businesses throughout Hawaii and customer reviews left on Google Maps. The data comes from two sources: a review dataset containing user reviews and a metadata dataset containing information about businesses such as category, location, price level, and average rating.

After merging the two datasets using `gmap_id`, the resulting dataset contains information about both businesses and the reviews they receive. Relevant columns for this project include:

* `rating`: the star rating given by a customer.
* `text`: the written review left by a customer.
* `pics`: whether the review includes pictures.
* `resp`: whether the business responded to the review.
* `category`: business categories associated with the restaurant.
* `num_of_reviews`: the number of reviews received by the business.
* `price`: the price level of the business.
* `time`: the time the review was posted.

This project is centered around the following question:

**What factors are associated with customer engagement in Hawaii restaurant reviews, and can we predict whether a customer will leave a written review?**

This question is interesting because written reviews provide much richer information than star ratings alone. Understanding what influences customers to leave written feedback can help businesses better understand customer behavior and engagement. Additionally, identifying factors associated with written reviews may help businesses encourage more detailed customer feedback.

The merged dataset contains approximately 1,505,011 reviews and business records.

---

## Cleaning and EDA


  <iframe
    src="./assets/dist-of-restaurant-review-ratings (1).html"
    width="900"
    height="600"
    frameborder="0">
  </iframe>
  
  <iframe
    src="./assets/avg-review-length.html"
    width="900"
    height="600"
    frameborder="0">
  </iframe>

  <iframe
    src="./assets/business-response-rate.html"
    width="900"
    height="600"
    frameborder="0">
  </iframe>

  <iframe
    src="./assets/number-of-reviews.html"
    width="900"
    height="600"
    frameborder="0">
  </iframe>
  
## Assessment of Missingness

Missingness Analysis

| Column | Percent Missing |
|----------|----------|
| resp | 93% |
| pics | 91% |
| state | 59% |
| price | 54% |
| text | 43% |
| description | 27% |
| hours | 15% |

| Rating | Missing Text Rate |
|----------|----------|
| 1 | 27% |
| 2 | 36% |
| 3 | 50% |
| 4 | 48% |
| 5 | 41% |


I investigated the missingness of the text column, which contains the written content of a review. Approximately 43% of reviews do not contain review text.

To determine whether this missingness was related to another variable in the dataset, I examined the proportion of missing review text across different rating levels. The results showed that the missingness rate varied substantially by rating. For example, only 27% of 1-star reviews were missing text, compared to roughly 50% of 3-star reviews.

This suggests that the missingness of review text depends on the observed variable rating. Therefore, the missingness is unlikely to be Missing Completely at Random (MCAR). Instead, it is more consistent with Missing At Random (MAR), since the probability that review text is missing appears to depend on the review's rating.


---

## Hypothesis Testing


  <iframe
    src="./assets/permutation-test.html"
    width="900"
    height="600"
    frameborder="0">
  </iframe>
  
Hypothesis Test 1: Review Length and Rating

Null Hypothesis:
Low-rated reviews (1–2 stars) and high-rated reviews (4–5 stars) have the same average review length.

Alternative Hypothesis:
Low-rated reviews have a greater average review length than high-rated reviews.

Test Statistic:
Difference in mean review length between low-rated and high-rated reviews.

Results:
The observed difference in mean review length was 80.40 characters. A permutation test with 1000 permutations produced a p-value less than 0.001.

Since the p-value is well below 0.05, we reject the null hypothesis. There is strong evidence that low-rated reviews tend to be substantially longer than high-rated reviews. This suggests that customers are more likely to leave detailed explanations when they have negative experiences.

---

Hypothesis Test 2: Business Responses and Rating

Null Hypothesis:
Businesses respond to reviews at the same rate regardless of review rating.

Alternative Hypothesis:
Businesses are more likely to respond to low-rated reviews than high-rated reviews.

Test Statistic:
Difference in response rates between low-rated and high-rated reviews.

Results:
The observed difference in response rates was 0.0301 (approximately 3 percentage points). A permutation test with 1000 permutations produced a p-value less than 0.001.

Since the p-value is well below 0.05, we reject the null hypothesis. There is strong evidence that businesses are more likely to respond to low-rated reviews than high-rated reviews. This suggests that businesses prioritize customer engagement when addressing negative experiences.

---

## Framing a Prediction Problem

Prediction Problem
The prediction problem for this project is to predict whether a customer will leave a written review in addition to a star rating. The response variable is has_text, which indicates whether a review contains written text. This is a binary classification problem because there are only two possible outcomes: a review either contains text or it does not.

I chose this prediction problem because it aligns closely with the overall theme of the project, which focuses on customer engagement and review behavior. In the exploratory analysis, I found that review behavior differs substantially across rating levels. Reviews with lower ratings were generally longer and more detailed, while higher-rated reviews were often shorter or contained no text. Additionally, the missingness analysis showed that the likelihood of review text being present depends on the rating, suggesting that written feedback is an important aspect of customer engagement.

The primary evaluation metric will be the F1-score. Since the classes are not perfectly balanced, F1-score is more informative than accuracy alone because it considers both precision and recall. This allows the model to be evaluated on its ability to correctly identify reviews that contain written text while also avoiding excessive false positives.

At the time of prediction, the model will only use information that would reasonably be available before observing whether the review contains text. Potential features include the review rating, whether pictures were attached, business category information, price level, business popularity measures such as the number of reviews, and temporal information derived from the review date. Features that directly depend on the presence of review text, such as review length, will not be used because they would not be available at the time of prediction.

---

## Baseline Model

My baseline model was a Decision Tree Classifier with a maximum depth of 3. The model used the features has_pics, has_response, num_categories, year, and month to predict whether a review contained written text (has_text). Model performance was evaluated using the F1-score, and the baseline model achieved an F1-score of 0.722.

---

## Final Model

To improve the model, I incorporated additional features including rating, num_of_reviews, and a numerical encoding of restaurant price level. These features were selected because they were plausibly related to customer engagement and review-writing behavior.

The final Decision Tree Classifier achieved an F1-score of 0.733, improving upon the baseline model. Feature importance analysis showed that the strongest predictor was whether the review contained pictures, followed by the year of the review and the review rating. This suggests that reviews with attached photos are substantially more likely to include written text as well.

---

## Fairness Analysis

| Price Group | F1 Score |
|----------|----------|
| Low Price ($, $$) | 0.732 |
| High Price ($$$, $$$$) | 0.748 |

To evaluate fairness, I compared model performance across restaurant price groups. Restaurants were divided into two groups based on price level: lower-priced restaurants (price levels 1–2) and higher-priced restaurants (price levels 3–4).

I evaluated model performance using the same metric used throughout the project, the F1-score. The model achieved an F1-score of approximately 0.732 on lower-priced restaurants and 0.748 on higher-priced restaurants.

Although the model performed slightly better on reviews associated with higher-priced restaurants, the difference in performance was relatively small (approximately 0.016). This suggests that the model performs similarly across restaurant price groups and does not appear to exhibit substantial disparities in predictive performance between lower-priced and higher-priced businesses.
