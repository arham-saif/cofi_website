+++
title = "Our Tech Stack"
description = "Some information about the technology we use to develop and deploy our products"
+++

## Why we use open-source

We're passionate about using open-source software (OSS) to develop specialised data solutions and tools for our clients. The benefits of using OSS as opposed to pre-packaged products are numerous; the most obvious two being flexibility and cost. We benefit from the rich ecosystem of open-source tech that we can bring together into a product that works for you. And you don't pay extortionate per-user licencing costs!

---

<i class="fab fa-4x fa-r-project"></i>

<a href="https://www.r-project.org/about.html" target="_blank">R</a> is the primary tool we use for everything from web-scraping and data-cleaning, to developing interactive web-apps and digital documents. Traditionally a programming language reserved for high-level statistical computing and graphics, R has evolved rapidly into the perfect tool for communicating best-in-its-class data programming and analytics with business-ready reports and dashboards - all from one language!

We draw heavily on the <a href="https://www.tidyverse.org/" target="_blank">`tidyverse`</a> suite of packages for all data wrangling and modelling, <a href="https://rmarkdown.rstudio.com/" target="_blank">`rmarkdown`</a> for reproducible reporting, and <a href="https://shiny.rstudio.com/" target="_blank">`shiny`</a> for interactive web-applications and dashboards powered by R on the back-end. Check out our [portfolio page](/portfolio) to see some of these in action.

---

## Deployment of Web-Applications

For an R-powered web-application to function, it needs to be hosted on a server with R and Shiny Server software installed. For applications that do not require any authentication, we can host the app for you on one of our own cloud servers with the option of mapping your own URL to the server.

For private apps, we use <a href="https://.rstudio.com/" target="_blank">RStudio's</a> <a href="https://www.shinyapps.io/" target="_blank">shinyapps.io</a> hosting service. This allows you an unlimited number for authenticated users as well as user level pemissions on the app - different users can have access to different levels of data. You can also map your own URL to this server!

### Self-Hosting

If you need your app to be hosted on your own system - talk to your IT team about R and Shiny-Server compatability within your organisation, you may already have the infrastructure in place. If not, <a href="https://.rstudio.com/" target="_blank">RStudio</a> offer a number of commercial products to allow for secure deployment of R-based reports and applications from within your company's firewall. Investing in an R infrastructure and experienced analytics developers for your business will most likely be a lot more effective and considerably cheaper than off-the-shelf BI solutions like Tableau or Power BI.

### <i class="fab fa-docker"></i> Docker

Another deployment option we offer is via <a href="https://www.docker.com/what-docker" target="_blank">Docker</a>. It's a method of bundling up all the software dependencies of an application into a 'container' which can be deployed on any linux server with docker installed - eliminating the need for your IT team to manage the installation and mainteneace of additional software. There's a good chance your IT team are already using docker so this could be the most efficient and cost-effective way of deploying an R application on your own system!

---

If you have any questions about our development and deployment process, get in touch via our [contant page](/contact).
