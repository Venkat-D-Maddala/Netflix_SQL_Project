# Data Analysis of NETFLIX's Movies and TV Shows using SQL 
## Project Overview
This project presents an in-depth analysis of Netflix's movie and TV show catalog using SQL. The primary aim is to derive actionable insights and address key business questions using the dataset. This README outlines the project's goals, business challenges, applied solutions, key insights, and overall conclusions.

## Project Objectives
Examine the distribution between movies and TV shows on the platform.

Determine the most frequently assigned ratings for both content types.

Analyze titles by release year, country of origin, and duration.

Categorize and explore content based on specific patterns and keyword-based conditions.

## 📂 Dataset  
The dataset used in this project is sourced from Kaggle:  
🔗 [Netflix Movies and TV Shows Dataset](https://www.kaggle.com/datasets/shivamb/netflix-shows?resource=download)

### 🧾 Schema  
```sql
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix (
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
