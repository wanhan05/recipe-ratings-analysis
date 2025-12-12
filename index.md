---
layout: default
title: Recipe Complexity and Ratings Analysis
---

# Recipe Complexity and Ratings Analysis

**By: Wanhan**

## Introduction

Have you ever wondered whether spending hours on an elaborate recipe is actually worth it? This project investigates the relationship between recipe complexity and user ratings using data from Food.com.

The dataset contains recipes and user interactions from Food.com. We merged the recipes dataset with user ratings to analyze whether more complex recipes — those with more steps, more ingredients, or longer cooking times — tend to receive higher or lower ratings. This question matters for home cooks deciding how much effort to invest, recipe developers designing new content, and anyone curious about what makes a recipe successful.

**Number of rows:** 83,782

### Relevant Columns

| Column | Description |
|--------|-------------|
| `name` | Recipe name |
| `id` | Unique recipe identifier |
| `minutes` | Minutes to prepare recipe |
| `n_steps` | Number of steps in recipe |
| `n_ingredients` | Number of ingredients in recipe |
| `avg_rating` | Average user rating (1-5 scale), computed from merged ratings data |
| `calories` | Calories per serving (extracted from nutrition) |

**Central Question:** What is the relationship between recipe complexity and average rating?

---

## Data Cleaning

We performed the following cleaning steps:

1. **Left merged** the recipes and interactions datasets on recipe ID.
2. **Replaced ratings of 0 with `NaN`** because the rating scale is 1-5; a rating of 0 indicates a missing rating (user left a review without selecting stars), not an actual score.
3. **Computed average rating** per recipe and added it back to the recipes dataset.
4. **Extracted nutrition components** from the `nutrition` column (stored as a list) into separate columns: `calories`, `total_fat`, `sugar`, `sodium`, `protein`, `saturated_fat`, and `carbohydrates`.
5. **Converted `submitted`** to datetime format.

Here is the head of the cleaned DataFrame:

| name | id | minutes | n_steps | n_ingredients | avg_rating | calories |
|------|-----|---------|---------|---------------|------------|----------|
| 1 brownies in the world best ever | 333281 | 40 | 10 | 9 | 4.0 | 138.4 |
| 1 in canada chocolate chip cookies | 453467 | 45 | 12 | 11 | 5.0 | 595.1 |
| 412 broccoli casserole | 306168 | 40 | 6 | 9 | 5.0 | 194.8 |
| millionaire pound cake | 286009 | 120 | 7 | 7 | 5.0 | 878.3 |
| 2000 meatloaf | 475785 | 90 | 17 | 13 | 5.0 | 267.0 |

---

## Univariate Analysis

<iframe src="assets/n_steps_dist.html" width="800" height="600" frameborder="0"></iframe>

The distribution of recipe steps is right-skewed, with most recipes containing between 5 and 15 steps. Very few recipes exceed 40 steps.

---

## Bivariate Analysis

<iframe src="assets/steps_rating_box.html" width="800" height="600" frameborder="0"></iframe>

The box plot shows that median ratings remain relatively consistent across complexity groups (around 4.6-4.7), suggesting that the number of steps alone may not strongly influence ratings. However, simpler recipes (1-5 steps) show slightly more variation in ratings.

---

## Interesting Aggregates

| steps_bin | avg_rating | minutes | n_ingredients | calories |
|-----------|------------|---------|---------------|----------|
| 1-5 | 4.638 | 67.78 | 6.94 | 330.24 |
| 6-10 | 4.616 | 124.80 | 8.78 | 403.54 |
| 11-15 | 4.618 | 117.40 | 10.32 | 466.14 |
| 16-20 | 4.638 | 135.29 | 11.50 | 542.26 |
| 21+ | 4.647 | 187.23 | 12.84 | 662.62 |

This table shows that while average ratings remain stable across complexity groups, more complex recipes tend to have longer cooking times, more ingredients, and higher calories. Interestingly, the most complex recipes (21+ steps) have the highest average rating at 4.647.

---

## Assessment of Missingness

### NMAR Analysis

We believe the `description` column (70 missing values) is likely **NMAR** (Not Missing At Random). Users may skip writing descriptions for very simple or obvious recipes where they feel no explanation is needed — meaning the missingness depends on the description content itself. Additional data that could help explain this missingness (making it MAR) would be information about the recipe submission interface, such as whether descriptions were required or optional at different time periods.

### Missingness Dependency

We analyzed whether the missingness of `avg_rating` (2,609 missing values) depends on other columns.

**Missingness depends on `n_steps`:**

We performed a permutation test comparing the mean number of steps for recipes with missing ratings versus those with ratings.

- **Null Hypothesis:** The distribution of `n_steps` is the same for recipes with and without missing ratings.
- **Alternative Hypothesis:** The distribution of `n_steps` differs between the two groups.
- **Test Statistic:** Difference in means
- **Significance Level:** α = 0.05

Result: Observed difference = 1.493, p-value ≈ 0.000. We reject the null hypothesis — the missingness of `avg_rating` **does depend on** `n_steps`. Recipes with missing ratings tend to have more steps.

<iframe src="assets/missingness_dist.html" width="800" height="600" frameborder="0"></iframe>

**Missingness does not depend on `protein`:**

We performed the same permutation test using the `protein` column.

- **Null Hypothesis:** The distribution of `protein` is the same for recipes with and without missing ratings.
- **Alternative Hypothesis:** The distribution of `protein` differs between the two groups.
- **Test Statistic:** Difference in means
- **Significance Level:** α = 0.05

Result: Observed difference = 1.287, p-value = 0.194. We fail to reject the null hypothesis — the missingness of `avg_rating` **does not depend on** `protein`.

---

## Hypothesis Testing

We tested whether recipe complexity affects average ratings.

**Null Hypothesis:** Recipes with high complexity (more than 9 steps) and recipes with low complexity (9 or fewer steps) have the same average rating distribution. Any observed difference is due to random chance.

**Alternative Hypothesis:** Recipes with high complexity have a different average rating distribution than recipes with low complexity.

**Test Statistic:** Difference in mean ratings (high complexity − low complexity)

**Significance Level:** α = 0.05

**Results:**
- High complexity recipes (n = 36,362): mean rating = 4.624
- Low complexity recipes (n = 44,811): mean rating = 4.627
- Observed difference: -0.0031
- P-value: 0.499

<iframe src="assets/permutation_test.html" width="800" height="600" frameborder="0"></iframe>

**Conclusion:** With a p-value of 0.499, we fail to reject the null hypothesis at the α = 0.05 significance level. The observed difference in mean ratings between high and low complexity recipes is not statistically significant. This suggests that the number of steps in a recipe does not have a meaningful effect on its average rating — users rate simple and complex recipes similarly.

**Justification:** We chose a permutation test because it makes no assumptions about the underlying distribution of ratings (which is heavily left-skewed). The difference in means is an appropriate test statistic for comparing central tendency between two groups. The median split at 9 steps creates reasonably sized groups for comparison.

---

## Framing a Prediction Problem

**Prediction Problem:** Predict whether a recipe will receive a high average rating (≥ 4.5 stars) based on its characteristics.

**Type:** Binary Classification

**Response Variable:** `high_rating` — a binary indicator where 1 means the recipe has an average rating of 4.5 or higher, and 0 means it has a rating below 4.5. We chose this variable because understanding what makes a recipe successful is valuable for recipe developers and home cooks.

**Evaluation Metric:** F1-score. We chose F1-score over accuracy because our classes are imbalanced (73% high-rated vs 27% not). Accuracy would be misleading — a model that predicts "high rating" for everything would achieve 73% accuracy but be useless. F1-score balances precision and recall, giving us a better measure of how well we identify both high and low-rated recipes.

**Features available at time of prediction:** At the time a recipe is posted (before any ratings exist), we would know:
- `n_steps` — number of steps
- `n_ingredients` — number of ingredients
- `minutes` — preparation time
- `calories`, `protein`, `sugar`, `sodium`, etc. — nutrition information

We would **not** know any rating-related information, as ratings come after the recipe is published.

---

## Baseline Model

**Model:** Logistic Regression

**Features (2 quantitative):**
- `n_steps` — number of steps in the recipe (quantitative)
- `n_ingredients` — number of ingredients (quantitative)

**Encoding:** Both features are quantitative, so no categorical encoding was needed. We applied `StandardScaler` to standardize the features to have mean 0 and standard deviation 1, which helps logistic regression converge properly.

**Pipeline:** We implemented the model using a scikit-learn `Pipeline` that first scales the features, then applies logistic regression.

**Performance:**
- Accuracy: 0.7475
- F1-score: 0.8555

**Assessment:** This baseline model is decent but not great. The accuracy of 74.75% is only slightly better than always predicting "high rating" (which would give ~73% accuracy). The F1-score of 0.8555 is reasonable, but there is room for improvement. The model only uses two complexity-related features — adding more features like cooking time and nutritional information could improve performance.

---

## Final Model

**Model:** Random Forest Classifier with class weight balancing

**New Features Engineered:**
- `steps_per_ingredient` — ratio of steps to ingredients, captures how detailed each ingredient's preparation is
- `calories_per_ingredient` — nutritional density per ingredient
- `complexity_score` — product of steps and ingredients, captures overall recipe complexity
- `time_per_step` — average time spent per step, indicates complexity of individual steps

These features capture relationships between variables that raw features miss. For example, a recipe with 20 steps and 5 ingredients is more complex per-ingredient than one with 20 steps and 20 ingredients.

**Additional Features:** `minutes`, `calories`, `protein` — cooking time and nutritional information available at time of posting.

**Preprocessing:** `QuantileTransformer` applied to all features to handle outliers (e.g., recipes with extreme cooking times) and normalize skewed distributions.

**Hyperparameter Tuning:** We used `GridSearchCV` with 5-fold cross-validation, tuning:
- `n_estimators`: [100, 200] — number of trees
- `max_depth`: [5, 10, 15] — tree depth to control overfitting
- `min_samples_split`: [5, 10, 20] — minimum samples to split a node

**Best Hyperparameters:**
- `n_estimators`: 200
- `max_depth`: 15
- `min_samples_split`: 5

**Performance Comparison:**

| Model | Accuracy | F1-score |
|-------|----------|----------|
| Baseline | 0.7475 | 0.8555 |
| Final | 0.6089 | 0.7290 |

**Analysis:** While the F1-score decreased, the final model is actually more useful. The baseline model predicted "high rating" for nearly every recipe (achieving high F1 by exploiting class imbalance). The final model with `class_weight='balanced'` correctly identifies low-rated recipes, as shown in the confusion matrix below. This is more valuable for understanding what makes recipes unsuccessful.

<img src="assets/confusion_matrix_balanced.png" alt="Confusion Matrix" width="600">

The model correctly identifies 1,342 low-rated recipes while still catching 8,543 high-rated ones. This tradeoff provides more insight into recipe success factors than simply predicting the majority class.

---

## Fairness Analysis

We analyzed whether our model performs fairly across recipes of different complexity levels.

**Group X:** Simple recipes (9 or fewer steps)

**Group Y:** Complex recipes (more than 9 steps)

**Evaluation Metric:** Precision — the proportion of predicted high-rated recipes that are actually high-rated. We chose precision because we want to know if the model is equally reliable at identifying high-rated recipes across both groups.

**Null Hypothesis:** Our model is fair. Its precision for simple recipes and complex recipes are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis:** Our model is unfair. Its precision for simple recipes is different from its precision for complex recipes.

**Test Statistic:** Difference in precision (simple − complex)

**Significance Level:** α = 0.05

**Results:**
- Precision (simple recipes): 0.7589
- Precision (complex recipes): 0.7527
- Observed difference: 0.0062
- P-value: 0.437

**Conclusion:** With a p-value of 0.437, we fail to reject the null hypothesis at the α = 0.05 significance level. There is no statistically significant difference in precision between simple and complex recipes. Our model appears to perform fairly across recipe complexity groups — it does not systematically favor simple or complex recipes when predicting high ratings.
