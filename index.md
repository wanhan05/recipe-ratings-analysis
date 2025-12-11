---
layout: default
title: Recipe Complexity and Ratings Analysis
---

# Recipe Complexity and Ratings Analysis

**By: Wanhan**

## Introduction

Have you ever wondered whether spending hours on an elaborate recipe is actually worth it? This project investigates the relationship between recipe complexity and user ratings using data from Food.com.

The dataset contains recipes and user interactions from Food.com. We merged the recipes dataset with user ratings to analyze whether more complex recipes — those with more steps, more ingredients, or longer cooking times — tend to receive higher or lower ratings. This question matters for home cooks deciding how much effort to invest, recipe developers designing new content, and anyone curious about what makes a recipe successful.

**Number of rows:** [RUN `recipes.shape[0]` AND FILL IN]

### Relevant Columns

| Column | Description |
|--------|-------------|
| `name` | Recipe name |
| `id` | Unique recipe identifier |
| `minutes` | Minutes to prepare recipe |
| `n_steps` | Number of steps in recipe |
| `n_ingredients` | Number of ingredients in recipe |
| `avg_rating` | Average user rating (1-5 scale), computed from merged ratings data |

**Central Question:** What is the relationship between recipe complexity and average rating?
