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
