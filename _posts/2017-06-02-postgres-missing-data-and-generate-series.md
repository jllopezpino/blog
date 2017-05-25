---
layout: post
title: Postgres, missing data and generated_series
excerpt: "Simple example of how to check for missing data on Postgres with generated_series"
tags: [databases, sql, postgres, data]
modified: 2017-06-02
comments: true
image:
    feature: old-days-of-technology.jpg
---

It's 2017, and people still underestimate relational databases and sqlalchemy.
Postgres is certainly the open source database that has the richest feature set.
One of those little gems is generate_series.
For example, I use it to quickly check for missing data in a dataset:

{% highlight sql %}
SELECT dates.generate_series::date, count(*)
FROM aw_click_performance awcp
RIGHT OUTER JOIN (
	SELECT * FROM generate_series(
		(SELECT min(date_of_ad_click) FROM aw_click_performance),
		current_date,
		interval '1 day')
) AS dates ON awcp.date_of_ad_click = dates.generate_series
GROUP BY 1 ORDER BY 1;
{% endhighlight %}
