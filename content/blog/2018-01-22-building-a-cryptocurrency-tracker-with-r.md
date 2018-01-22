---
title: Building a Cryptocurrency Tracker with R
author: Paul Campbell
date: '2018-01-22'
slug: building-a-cryptocurrency-tracker-with-r
twitterImg: img/crypto_tracker.png
description: "Keep track of your personal cryptocurrency investments with our tracker tool..."
categories: []
tags:
  - dashboards
  - R
  - open data
  - shiny
---

### TL;DR - check the tracker out [here](https://cultureofinsight.shinyapps.io/crypto_tracker/).

<a href="https://cultureofinsight.shinyapps.io/crypto_tracker/" target="_blank"><img src="/img/crypto_tracker.png" style="width: 100%;" /></a>

As a recent cryptocurrency 'Investor' (*0.13 ETH baby*) I wanted to build a light tracker tool that could help me keep up with the mad market volatility in a more personalised manner.

Of course, there's no better tool for this task than the open-source programming language R, and the multitude of packages built for it that allows programmers with very modest levels of front-end development and sys-admin knowledge to go from nada to fully deployed, production quality web-application in a few hours. It really is a marvel.

### Functionality

The desired functionality I set out to build into the application was thus:

- Scrape up to date crypto data from the web whenever the app is launched.
- Convert USD closing prices to GBP using recent exchange rate, also scraped from the web on app launch.
- Compute the users coin value and ROI based on the user defined parameters of: what cryptocurrency, how much of it they have, when they bought it, and what currency they want there returns to be reported in (USD or GBP).
- Visualise the results in 3 KPI boxes and one interactive time-series chart.
- Laugh/Cry at how much money you've gained/lost in the last 24 hours.

### The Process

For a 'tracker' to be of any use, it needs to have up-to-date data being fed into it, ideally pulled in on a self-serving basis. That is, app functionality to go and get the data we want, when we want it, without requiring any user input or local data storage/maintenance.

This means programming a web-scrape to trigger every time the app is launched, which was made easy by the [crypto R package](https://github.com/JesseVent/crypto/) that scrapes data from [coinmarketcap](https://coinmarketcap.com/), providing the daily USD closing prices of any coins of our choosing (I opted to stick to BTC and ETH).

Once the data is there, we utilise the powerful duo of [shiny](https://shiny.rstudio.com/) and the [tidyverse](https://www.tidyverse.org/) by providing reactive user input variables to data wrangling functions on the back end to compute values and ROI figures specific to the user. With the data now tailored to the needs of the end user, the results are visualised in a responsive HTML user interface that will look the part on desktop, tablet or mobile. _fin_.

If you're interested in seeing the source code behind the app, you can check it over on [github](https://github.com/PaulC91/crypto_tracker).

### Why am I telling you this?

Good question! Well, if you've made it this far, you might be interested in a bespoke web-application of your own. At Culture of Insight, we've found that R usually succeeds where expensive off-the-shelf BI software falls short.

We can build tools that _do things_ with data rather than simply presenting it. So if you have any ideas that you think we could help with, please do [get in touch](https://cultureofinsight.com/contact/).

Here's to the crypto being +20% rather than -20% today.