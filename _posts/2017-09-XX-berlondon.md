---
layout: post
title: Berlondon or when it rains more in Berlin than in London
excerpt: "Comparing precipitation data in Berlin and London"
tags: [berlin, london, weather]
modified: 2017-XX-XX
comments: true
image:
    feature: old-days-of-technology.jpg
---

## Dataset

Finding historic climate data was harder than I thought.
The best source that I found was the [ECA (European Climate Assesment) dataset](http://eca.knmi.nl/dailydata/index.php) dataset, although data was published with quite some delay.


The way of accessing the data is a bit cumbersome.
You have to complete a form in the website and wait a few seconds until they generate a zipfile with the content. The good side is that in a single file they provide you with all the historic data for the city and the metric selected.


This is exactly what they offer for the precipitation data:
- Station identifier
- Source identifier
- Date in format YYYYMMDD
- Precipitation amount in 0.1 mm
- Quality code (for the precipitation amount)
