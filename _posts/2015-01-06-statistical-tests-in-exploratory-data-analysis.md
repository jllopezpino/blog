---
layout: post
title: Exploratory data analysis&#58; statistical tests
excerpt: "This is the first post of a series of posts on exploratory data analysis and it covers one of the most overlooked parts: statistical testing."
tags: [data science, exploratory data analysis, statistical tests]
modified: 2015-01-06
comments: true
image:
    feature: old-days-of-technology.jpg
---


I want to start a series of posts on *exploratory data analysis* and one of the most overlooked part during data analysis is statistical testing.

I recommend to use the following table by [Pamela J. Ludford](http://www-users.cs.umn.edu/~ludford/stat_overview.htm) (University of Minnesota) as a cheatsheet:


<style>
table{
    border-collapse: collapse;
    border-spacing: 0;
    border:2px solid #000000;
}
th{
    border:2px solid #000000;
}

td{
    border:1px solid #000000;
}
</style>
||| Dependent variable |
||| Categorical  | Continuous  |
||| ------------- | ------------- |
|**Independent variable** | Categorical | Chi Square  | t-test, ANOVA  |
|| Continuous | LDA, QDA  | Regression |


I have chosen an [old credit dataset](http://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) from Professor Dr. Hans Hofmann (University of Hamburg) for the simple reasons that I'm more familiar with this domain and the set of attributes is nice.
