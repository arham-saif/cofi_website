---
title: UK Crime Mapping Tool
twitterImg: img/crime-watch.png
author: Paul Campbell
date: '2018-05-16'
---

<a href="https://apps.cultureofinsight.com/crime-watch/" target="_blank"><img src="/img/crime-watch.png" alt="UK Police data interface" width="100%" title="Click to launch app" /></a>

This is an example of how we can 'democratise' a data source by combining back-end data programming with a simple, effective user interface.

The application uses geocoding and GPS to allow users to retrieve and map data from the UK Police data base at a location of their choice within England, Wales or Northern Ireland.

Currently the app will return data for the most recent month available in the data base (March 2018 at the time of writing). Functionality could be added to allow users to request data from previous months.

Criminal incidents within a 1 mile radius of the users chosen location are retrieved and mapped at the lattitude + longitude recorded by the police. A summary count of all incidents, grouped by category, is also visualised in a bar chart.

Visit the full-page app at <a href="https://apps.cultureofinsight.com/crime-watch/" target="_blank">apps.cultureofinsight.com/crime-watch/</a> or check out the code <a href="https://github.com/PaulC91/crime-watch" target="_blank">here</a>.

---

## Credits

* Geocoding via the [rOpenSci opencage R package](https://github.com/ropensci/opencage)
* GPS locater via Dr Tom August's [shiny geolocation Javascript script](https://github.com/AugustT/shiny_geolocation) 
* API calls to the [data.police.uk](https://data.police.uk/) data base via Nicholas Tierney's [ukpolice R package](https://github.com/njtierney/ukpolice)
